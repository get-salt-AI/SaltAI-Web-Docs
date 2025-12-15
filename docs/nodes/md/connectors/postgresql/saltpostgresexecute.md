# PostgreSQL Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes non-SELECT SQL statements (INSERT, UPDATE, DELETE, DDL) on a PostgreSQL database. It connects using a credentials URI and returns a human-readable result summary along with machine-friendly JSON and tabular renderings where applicable.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to perform write or administrative operations against a PostgreSQL database, such as inserting records, updating rows, deleting data, or running DDL (e.g., CREATE TABLE). Provide a valid PostgreSQL credentials URI, set an appropriate timeout, and supply the SQL statement to execute. Typically placed after configuration/credential nodes and before any nodes that depend on the execution outcome.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">PostgreSQL connection URI specifying how to connect to the database.</td><td style="word-wrap: break-word;">postgres://<username>:<password>@db.example.com:5432/my_database</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the database operation before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL statement to execute. Intended for INSERT/UPDATE/DELETE or other non-SELECT operations.</td><td style="word-wrap: break-word;">INSERT INTO users (name, email) VALUES ('John Doe', 'john@example.com')</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the execution outcome (e.g., rows affected).</td><td style="word-wrap: break-word;">PostgreSQL Execute Results: 1 row affected</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted response containing structured details about the operation result.</td><td style="word-wrap: break-word;">{"status":"success","rows_affected":1}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML table representation of the result when available. For non-SELECT statements, this may be an empty or minimal table.</td><td style="word-wrap: break-word;"><table><thead><tr><th>rows_affected</th></tr></thead><tbody><tr><td>1</td></tr></tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX data of the result when applicable. Often minimal or empty for non-SELECT statements.</td><td style="word-wrap: break-word;"><base64-xlsx-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF data of the result when applicable. Often minimal or empty for non-SELECT statements.</td><td style="word-wrap: break-word;"><base64-pdf-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Non-SELECT focus**: This node is intended for INSERT/UPDATE/DELETE/DDL statements; use query-oriented nodes for SELECT operations.
- **Credentials required**: A valid PostgreSQL credentials URI must be provided or the node will fail.
- **Timeouts**: Long-running statements should have an appropriately increased timeout to avoid premature failure.
- **Safety**: Avoid concatenating untrusted input into SQL to prevent SQL injection vulnerabilities.
- **Result formats**: Tabular/Excel/PDF outputs may be minimal for non-SELECT operations since they typically produce metadata rather than row sets.
- **Permissions**: Ensure the provided database user has sufficient privileges for the intended operation.

## Troubleshooting
- **Invalid credentials or URI**: Verify the credentials_path format and values (host, port, database, username, password).
- **SQL syntax error**: Validate the sql_text against PostgreSQL syntax; test the statement directly in a SQL client.
- **Timeouts**: Increase the timeout input for heavy operations or ensure the database is responsive.
- **Insufficient privileges**: Confirm the database user has the necessary permissions to execute the statement.
- **Connectivity issues**: Check network access, firewall rules, and that the PostgreSQL instance is reachable on the specified host/port.
