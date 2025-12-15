# MySQL Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes non-SELECT SQL statements (INSERT, UPDATE, DELETE, DDL) against a MySQL database using provided credentials. Returns a human-readable summary and a JSON payload of the execution result. Useful for data modification and administrative operations rather than data retrieval.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to modify data or schema in a MySQL database as part of a workflow (e.g., insert new records, update fields, delete rows, or run DDL statements). Provide a valid credentials path and your SQL statement. The node performs the request and returns a formatted text summary along with a JSON result that can be consumed by downstream nodes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Path or reference to the stored MySQL credentials that the node will use to authenticate.</td><td style="word-wrap: break-word;"><path-to-mysql-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the execution request to complete before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL statement to execute. Intended for INSERT/UPDATE/DELETE statements and DDL (e.g., CREATE TABLE, ALTER TABLE).</td><td style="word-wrap: break-word;">INSERT INTO users (name, email) VALUES ('John Doe', 'john@example.com')</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Formatted summary of the execution (e.g., title and affected rows or status message).</td><td style="word-wrap: break-word;">MySQL Execute Results: 1 row affected</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string with the raw execution result returned by the service (e.g., affected row count, status, or error).</td><td style="word-wrap: break-word;">{"rows_affected": 1, "status": "success"}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML output placeholder. For execute operations, this will typically be empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary XLSX data placeholder. For execute operations, this will typically be empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF data placeholder. For execute operations, this will typically be empty.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Write operations only**: This node is intended for non-SELECT statements. Use the MySQL Query node for data retrieval.
- **Credentials required**: The node will fail if the credentials path is missing or invalid.
- **Execution timeout**: Long-running statements may time out; adjust the timeout input as needed.
- **Result content**: The text output provides a human-readable summary, while the JSON output contains the structured result. The HTML/XLSX/PDF outputs are typically empty for execute operations.
- **SQL safety**: Ensure your SQL is parameterized or sanitized in upstream logic to avoid injection risks.

## Troubleshooting
- **SQL syntax error**: Verify the SQL statement in sql_text is valid for MySQL and that table/column names are correct.
- **Authentication/permission denied**: Confirm the credentials_path points to valid MySQL credentials and that the user has permissions to perform the operation.
- **Timeouts**: Increase the timeout if the database is under heavy load or the statement is expected to take longer.
- **Connectivity issues**: Check network access, firewall rules, and that the MySQL server is reachable from the execution environment.
- **No rows affected**: This may indicate that the WHERE clause matched no records. Double-check your conditions.
