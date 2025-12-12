# PostgreSQL Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs non-SELECT SQL statements (INSERT, UPDATE, DELETE, DDL) against a PostgreSQL database through the Salt CData service. It loads your configured PostgreSQL credentials, sends the statement to the service's execute endpoint, and returns a human-readable summary plus the raw JSON response.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to modify data or perform administrative SQL actions on PostgreSQL (e.g., insert rows, update records, delete rows, create/alter tables). Provide your SQL statement, a reference to valid PostgreSQL credentials, and an optional timeout. Often used alongside a query node to verify results after changes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the stored PostgreSQL credentials compatible with the 'postgres' template.</td><td style="word-wrap: break-word;">/projects/<project-id>/secrets/postgres.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the operation before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A single SQL statement to execute. Intended for INSERT/UPDATE/DELETE and other non-SELECT statements.</td><td style="word-wrap: break-word;">INSERT INTO users (name, email) VALUES ('John Doe', 'john@example.com')</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the execution result.</td><td style="word-wrap: break-word;">PostgreSQL Execute Results: affected_rows=1</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON string of the execution response (often includes affected row count or service messages).</td><td style="word-wrap: break-word;">{"status":"ok","affected_rows":1}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML output (not typically used for execute operations; returned as empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary XLSX data (not used for execute operations; returned as empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF data (not used for execute operations; returned as empty).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Non-SELECT only**: This node is designed for INSERT/UPDATE/DELETE and DDL statements. Use the PostgreSQL Query node for SELECT queries.
- **Credentials**: Requires valid PostgreSQL credentials matching the 'postgres' credential template.
- **Result content**: The JSON output typically includes status information and may include affected row counts depending on the service response.
- **SQL safety**: Avoid building SQL from untrusted input to prevent SQL injection.
- **Timeouts**: Long-running operations may require a higher timeout value.

## Troubleshooting
- **Syntax errors**: If you receive a syntax error, validate the SQL against your PostgreSQL version and ensure the statement is non-SELECT.
- **Permission denied**: Ensure the database user in your credentials has appropriate privileges for the targeted tables and operations.
- **Connection or timeout issues**: Verify the credentials reference, network accessibility, and increase the timeout if the operation is expected to take longer.
- **Schema or table not found**: Fully qualify objects with schema (e.g., public.users) or verify the default search_path in your environment.
- **No rows affected**: Check WHERE clauses for UPDATE/DELETE statements and confirm target data matches your conditions.
