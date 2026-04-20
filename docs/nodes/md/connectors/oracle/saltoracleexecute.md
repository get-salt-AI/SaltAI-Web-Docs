# Oracle Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node sends non-SELECT SQL statements (INSERT, UPDATE, DELETE and other DML) to an Oracle database through a configured Oracle service. It uses shared database-node behavior for authentication, request execution and output formatting. The node returns both a human-readable summary and structured JSON of the execution result, such as affected row counts or status details.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracleexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltOracleExecute whenever you need to modify data in an Oracle database from your Salt workflow—for example inserting new records, updating existing rows, or deleting data. It typically appears after a credential/configuration node or as part of a data pipeline that first reads data (via SaltOracleQuery or other sources), transforms it, and then persists changes to Oracle. Upstream, you will usually provide: (1) a credentials path produced or referenced by a shared database credential/config node, and (2) a timeout or configuration node that defines API time limits, and (3) optionally an upstream text/composition node that dynamically builds the SQL statement. Downstream, nodes that consume the output commonly include logging/notification nodes (to report success/failure), decision/control-flow nodes (to branch on success status), or further database nodes for follow-up checks (e.g., SaltOracleQuery to verify changes). This node integrates with the shared CData database base node infrastructure, reusing its HTTP request mechanism and credential handling. For best results, (1) keep SQL statements parameterized and validated upstream where possible, (2) test SQL against a staging database before connecting the node to production credentials, (3) centralize credentials in one place and reference them via the credentials_path, and (4) set realistic timeouts based on the complexity and expected duration of your DML operations.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filesystem or configuration path pointing to the Oracle credential template named "oracle". This is used by the shared database base node to load connection details (such as host, port, service name/SID, username, and password or secure token). The path must reference a valid and accessible credential file or entry; otherwise the node will fail to authenticate. Keep this path stable and secure and avoid hardcoding any secrets in the SQL text.</td><td style="word-wrap: break-word;">/config/credentials/oracle/prod_oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the Oracle execute request to complete. If the execution takes longer than this timeout, the request will be aborted and an error will be returned. Set higher values for heavy batch updates and lower values for simple single-row DML to fail fast on connectivity issues.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Oracle SQL statement to execute, intended for data modification operations such as INSERT, UPDATE, DELETE, or other non-SELECT DML. The string supports multiline SQL and is not treated as a dynamic prompt. It should be valid Oracle SQL, and any parameters (e.g., :id, :first_name) should either be inlined beforehand or handled by the backing service according to your setup. Avoid including secrets directly in the SQL string.</td><td style="word-wrap: break-word;">UPDATE employees SET salary = salary * 1.05 WHERE department_id = 50</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable summary of the execution result, produced by the shared database base node's default formatting. This often includes information such as whether the execution succeeded, how many rows were affected, and a short status message. It is suitable for logging, displaying to users, or feeding into LLM reasoning nodes.</td><td style="word-wrap: break-word;">Oracle Execute Results: Success. 42 rows affected by UPDATE statement.</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">A structured JSON representation of the Oracle execute response, as returned by the CData/Oracle backend and normalized by the database base node. This may include fields like status, rows_affected, execution_time_ms, and any error details when applicable. Downstream nodes can parse this JSON to implement conditional logic, metrics collection, or further processing.</td><td style="word-wrap: break-word;">{'title': 'Oracle Execute Results', 'status': 'success', 'operation': 'UPDATE', 'rows_affected': 42, 'execution_time_ms': 187, 'request': {'endpoint': '/execute', 'query': 'UPDATE employees SET salary = salary * 1.05 WHERE department_id = 50'}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Large batch INSERT/UPDATE/DELETE operations can take significant time; adjust the timeout input accordingly and consider batching large jobs into multiple smaller statements.
- **Limitations**: This node is intended for non-SELECT operations; while you technically can send any SQL text, SELECT queries are better handled by SaltOracleQuery to get rowset-oriented outputs.
- **Behavior**: The node relies on a shared database base node that handles HTTP calls to a backing Oracle service; connectivity issues, misconfigured endpoints, or invalid credentials at that layer will surface as execution errors here.
- **Behavior**: The SQL is sent as-is in the "query" field of the request payload; there is no automatic escaping or validation in this node, so always validate or sanitize upstream if accepting user-generated input.

## Troubleshooting
- **Invalid credentials or connection errors**: If you see errors indicating authentication failure, unreachable host, or similar connection issues, verify that `credentials_path` points to a valid Oracle credential configuration and that the referenced database is reachable from the service environment.
- **SQL syntax or Oracle errors**: Errors like `ORA-00936: missing expression` or other `ORA-` codes usually mean the SQL in `sql_text` is invalid. Test the exact statement in an Oracle client (e.g., SQL*Plus or SQL Developer), correct the syntax, and then update the node input.
- **Timeout exceeded**: If executions fail with timeout messages, your `timeout` value may be too low for the complexity or volume of the DML operation. Increase the timeout or break the work into multiple smaller statements.
- **Unexpected zero rows affected**: When the node reports success but `rows_affected` is 0, double-check the WHERE clause and conditions in your SQL. The statement ran, but no rows matched the criteria; confirm that you are targeting the correct schema, table, and filters.
