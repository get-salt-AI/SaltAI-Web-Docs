# Oracle Table Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltOracleTableInfo is an Oracle metadata node built on the shared CData DatabaseBaseNode. It connects to an Oracle database using stored Oracle credentials and calls the Oracle CData service to fetch information about tables and their schema. The node returns the metadata in a text-friendly form plus a structured JSON object suitable for downstream processing and automation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracletableinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltOracleTableInfo when you need to inspect or dynamically work with the structure of an Oracle database in a Salt workflow. Typical scenarios include: (1) discovering available tables and columns before building queries, (2) generating dynamic forms or prompts based on table schema, and (3) validating or documenting database structures as part of data pipelines.

This node usually appears early in an Oracle-related workflow, before nodes like SaltOracleQuery and SaltOracleExecute. Upstream, you will typically provide credentials via a generic credential path selector or credentials node that stores the Oracle connection details used by all Oracle CData nodes. Downstream, its JSON output is commonly consumed by prompt-building nodes (to inform an LLM about available tables and fields), branching/logic nodes that select tables dynamically, or SQL-construction nodes that assemble queries based on the discovered schema.

Internally it shares behavior with other CData database nodes like SaltOracleQuery and SaltOracleExecute: it relies on DatabaseBaseNode to load credentials, manage timeouts, make HTTP calls to the CData Oracle service, and normalize the response. Prefer this node whenever you need table and column information rather than row data. Combine it with SaltOracleQuery to first discover the schema and then safely query the relevant tables.

## Inputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 15%;">
<col style="width: 10%;">
<col style="width: 15%;">
<col style="width: 30%;">
<col style="width: 30%;">
</colgroup>
<thead><tr><th>Field</th><th>Required</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filesystem or key-path reference to the stored Oracle credentials configuration. This must point to a credential bundle that matches the "oracle" credential template expected by CData (typically containing host, port, service name/SID, username, and password or wallet configuration). The same path should be reused across SaltOracle* nodes so they share the same Oracle connection.</td><td style="word-wrap: break-word;">/opt/salt/credentials/oracle/hr-db.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds the node will wait for the Oracle CData service to respond. If the CData backend cannot return the table info within this window, the request fails with a timeout error. Use larger values for schemas with thousands of tables or when the CData service is accessed over a slow network.</td><td style="word-wrap: break-word;">60</td></tr>
</tbody>
</table>
</div>

## Outputs

<div style="overflow-x: auto;">
<table style="table-layout: fixed; width: 100%;">
<colgroup>
<col style="width: 20%;">
<col style="width: 20%;">
<col style="width: 35%;">
<col style="width: 25%;">
</colgroup>
<thead><tr><th>Field</th><th>Type</th><th>Description</th><th>Example</th></tr></thead>
<tbody>
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the retrieved Oracle table information. Typically includes a concise, formatted description such as the list of tables and selected columns so it can be easily inspected in logs or consumed by text-oriented nodes (e.g., prompt builders or LLM input nodes).</td><td style="word-wrap: break-word;">Oracle Table Info:\n- EMPLOYEES (EMPLOYEE_ID NUMBER, FIRST_NAME VARCHAR2(20), LAST_NAME VARCHAR2(25), HIRE_DATE DATE)\n- DEPARTMENTS (DEPARTMENT_ID NUMBER, DEPARTMENT_NAME VARCHAR2(30))</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured JSON payload returned by the CData Oracle service describing tables and their columns. The exact schema depends on the CData backend, but typically includes table names, column names, data types, nullability, and possibly primary key information. This is the preferred output for downstream automation, SQL generation, or validation logic.</td><td style="word-wrap: break-word;">{"tables": [{"name": "EMPLOYEES", "columns": [{"name": "EMPLOYEE_ID", "type": "NUMBER", "nullable": false, "primary_key": true}, {"name": "FIRST_NAME", "type": "VARCHAR2(20)", "nullable": true}, {"name": "LAST_NAME", "type": "VARCHAR2(25)", "nullable": false}, {"name": "HIRE_DATE", "type": "DATE", "nullable": false}]}, {"name": "DEPARTMENTS", "columns": [{"name": "DEPARTMENT_ID", "type": "NUMBER", "nullable": false, "primary_key": true}, {"name": "DEPARTMENT_NAME", "type": "VARCHAR2(30)", "nullable": false}]}]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Fetching metadata on large Oracle schemas (hundreds or thousands of tables) can be slow; increase the timeout input and consider narrowing the CData configuration to specific schemas if available.
- **Limitations**: The exact structure of the JSON output is determined by the CData Oracle connector; some advanced Oracle features (e.g., object types, materialized views, or synonyms) may be represented differently or omitted depending on connector configuration.
- **Behavior**: This node shares credentials and connection behavior with other CData nodes via DatabaseBaseNode. Changing the credentials_path affects all downstream Oracle interactions that reuse the same path.
- **Behavior**: Errors from the underlying CData or Oracle services (e.g., invalid user, insufficient privileges, or unreachable host) are surfaced through this node; make sure the Oracle user has privileges to query system catalog or metadata views.

## Troubleshooting
- **"Connection failed" or authentication errors**: Verify that credentials_path points to a valid Oracle credential file using the "oracle" template, and confirm the username, password, host, port, and service name/SID are correct. Ensure any required network routes or VPNs are active.
- **"Timeout exceeded while requesting Oracle table info"**: Increase the timeout input value, especially for large schemas. Also check network latency between the Salt environment and the CData Oracle service, and reduce schema size or filter metadata on the CData side when possible.
- **Empty or partial table list in JSON output**: Confirm that the Oracle user has sufficient privileges (e.g., access to ALL_TABLES/USER_TABLES views). If using multiple schemas, ensure the CData configuration is not restricting visible schemas unintentionally.
- **Unexpected JSON structure downstream**: Inspect the raw json output field to confirm its shape and adjust downstream nodes or mappings accordingly, as different CData versions or configurations can slightly change field names or nesting.
