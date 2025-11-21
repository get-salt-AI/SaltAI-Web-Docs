# Oracle Execute

Runs non-SELECT SQL statements against an Oracle database. Use it to perform INSERT, UPDATE, DELETE, DDL, or other execute-style commands. It loads credentials, sends the SQL to the Oracle service, and returns a formatted text summary plus a JSON result, with optional export placeholders.

## Usage

Use this node when you need to change data or run DDL in Oracle (e.g., insert new rows, update fields, delete data, create/alter tables). In a workflow, provide the credentials file path and a reasonable timeout, then pass a single SQL statement in sql_text. Downstream nodes can consume the text summary or the JSON payload for logging, branching, or UI display.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the Oracle credentials file to authorize the request.</td><td style="word-wrap: break-word;">/secure/creds/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the operation before failing.</td><td style="word-wrap: break-word;">120</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A single SQL statement to execute (non-SELECT). Supports INSERT, UPDATE, DELETE, and DDL.</td><td style="word-wrap: break-word;">UPDATE employees SET salary = salary * 1.05 WHERE department_id = 50</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the execution result.</td><td style="word-wrap: break-word;">Oracle Execute Results: 3 rows affected.</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted details from the execution (e.g., affected row count, status, messages).</td><td style="word-wrap: break-word;">{"row_count": 3, "status": "success"}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML output placeholder (usually empty for execute operations).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Binary-export placeholder for Excel data (usually empty for execute operations, base-compatible).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Binary-export placeholder for PDF data (usually empty for execute operations, base-compatible).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Non-SELECT only**: This node is intended for execute-style statements (INSERT/UPDATE/DELETE/DDL). For SELECT queries, use the Oracle Query node.
- **Single statement**: Provide one complete SQL statement in sql_text.
- **Credentials required**: The credentials_path must point to a valid Oracle configuration with connectivity and permissions to run the statement.
- **Timeouts**: Long-running operations can be terminated if they exceed the configured timeout.
- **Bind variables**: The node sends the SQL string as-is. If using bind variables (e.g., :id), ensure your SQL is valid as provided and any bindings are handled in your SQL or avoid using placeholders.

## Troubleshooting
- **Authentication or connection failure**: Verify credentials_path points to a valid file and that host/port/service/SID are correct. Ensure the database is reachable from the runtime environment.
- **SQL syntax error**: Check the sql_text for Oracle syntax compliance. Try running the statement directly against the database to validate.
- **Insufficient privileges**: The database user may lack permissions for the targeted table or DDL. Use a user with the required roles/privileges.
- **Timeouts**: Increase the timeout input for long-running operations or optimize the SQL/constraints to reduce execution time.
- **No rows affected**: Confirm the WHERE clause and target data. Log or inspect the JSON output to see row_count and messages.
