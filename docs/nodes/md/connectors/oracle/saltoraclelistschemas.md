# Oracle List Schemas

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node connects to an Oracle database using a configured credential profile and retrieves the list of accessible schemas. It invokes the Oracle service’s list-schemas operation and formats the response into both a human-readable summary and a JSON payload. It is intended for quick schema introspection before exploring tables or constructing queries.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclelistschemas.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover which schemas exist and are accessible in an Oracle database before selecting specific tables or building queries. A typical workflow is: configure and store Oracle credentials, optionally validate connectivity with Oracle Test Connection or Oracle Database Info, then run Oracle List Schemas to enumerate accessible schemas. The resulting schema names can be used as inputs to Oracle List Tables, Oracle Table Info, Oracle Query, Oracle Complex Query, Oracle Visual Query Builder, or Oracle Query Builder Helper. Place this node early in database-focused pipelines to drive dynamic UI selectors, schema exploration steps, or automatic metadata collection. For best practices, log or inspect the json output to understand its structure, and use that to drive conditional logic (for example, only proceed if a required schema like HR or SALES is present).

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reference to the stored Oracle credentials that the node will use to authenticate and connect. This is typically a path or identifier inside Salt’s credential storage and must match a profile compatible with the credential_template "oracle". The credentials should include host, port, user, password, and any other required Oracle connection parameters. If this value is incorrect or points to incomplete credentials, the service call will fail.</td><td style="word-wrap: break-word;">secrets/oracle/prod-hr-readonly.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of seconds to wait for the Oracle list-schemas request to complete. If the Oracle instance or network is slow, set a higher value to avoid premature timeouts. If set too low, you may see timeout errors; if set extremely high, failures may take longer to surface in your workflow.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-friendly textual summary of the schemas returned by Oracle. This is suitable for display in logs, dashboards, or inspector views and typically includes a title and a list of schema names.</td><td style="word-wrap: break-word;">Oracle Schemas - HR - SALES - FINANCE - SYS Total schemas: 4</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded representation of the Oracle list-schemas response. Commonly includes an array of schema objects and may include counts or additional metadata depending on the backend. Use this for programmatic processing, filtering, or driving downstream nodes.</td><td style="word-wrap: break-word;">{"schemas": [{"name": "HR", "owner": "HR"}, {"name": "SALES", "owner": "SALES"}], "schema_count": 2}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML-formatted view of the schema list, when provided by the shared database base node. Useful for embedding into rich dashboards or reports that can render HTML tables or lists.</td><td style="word-wrap: break-word;"><h2>Oracle Schemas</h2><ul><li>HR</li><li>SALES</li><li>FINANCE</li></ul></td></tr>
<tr><td style="word-wrap: break-word;">file</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A file path or identifier pointing to an exported version of the results (for example, a CSV or spreadsheet) when exports are enabled by the base node. This is intended for workflows that pass data into file-based processing or sharing steps.</td><td style="word-wrap: break-word;">exports/oracle/list_schemas_2024-05-10_134500.xlsx</td></tr>
<tr><td style="word-wrap: break-word;">meta</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded metadata about the operation, such as endpoint name, execution duration, or status flags. This is helpful for logging, monitoring, and diagnostics in larger workflows.</td><td style="word-wrap: break-word;">{"endpoint": "/list-schemas", "duration_ms": 420, "database": "ORCL", "status": "success"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: On large or busy Oracle instances, listing schemas may take longer than expected; if you encounter intermittent timeouts, gradually increase the timeout input and consider running schema discovery during off-peak hours.
- **Limitations**: The node only returns schemas that the connected Oracle user has permission to see. If the account has restricted privileges or limited access to data dictionary views, the schema list may not represent the full database.
- **Behavior**: This node does not take any schema filter or query text; it always calls the backend /list-schemas operation with an empty parameter body, relying entirely on the Oracle service and user permissions to determine what is returned.
- **Behavior**: Output formatting is handled by a shared database base node, ensuring that you consistently receive both readable text and JSON; this makes it straightforward to plug the node into existing database workflows that expect a standard output layout.

## Troubleshooting
- **Empty schema list**: If the text output shows "Oracle Schemas" but no entries, confirm that the credentials_path points to an Oracle user with privileges to view schemas. You may need to use a more privileged account or request additional grants from your database administrator.
- **Timeout or connection errors**: Errors indicating a timeout or inability to reach the Oracle service are often caused by network issues, incorrect host or port in the stored credentials, or a heavily loaded database. Verify the credential details, ensure the database is reachable from your environment, and increase the timeout value if necessary.
- **Authentication or authorization failures**: Messages about invalid username or password or insufficient privileges indicate problems with the stored credentials or user rights. Update the credential profile referenced by credentials_path with valid Oracle credentials and verify that the user can access schema metadata.
- **Unexpected JSON shape in downstream nodes**: If a downstream node fails when parsing the json output, first inspect the raw json string (for example by logging it) to confirm field names and structure, then adjust your downstream logic to align with those keys, such as "schemas" and "schema_count".
