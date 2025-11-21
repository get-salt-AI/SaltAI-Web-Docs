# SaltPostgresExecute

Executes PostgreSQL data-manipulation SQL (INSERT, UPDATE, DELETE) against a configured database via Salt's CData service. Returns a human-readable summary and the raw JSON response, typically including rows affected and status details.

## Usage

Use this node when you need to modify data in a PostgreSQL database as part of your workflow, such as inserting new records, updating existing ones, or deleting rows. Provide valid database credentials and a DML SQL statement; the node will execute it and emit both a summary string and a JSON payload for downstream logic or logging.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the stored PostgreSQL credentials that the node will use to authenticate with the database.</td><td style="word-wrap: break-word;">secrets/postgres.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the execution request before it times out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL statement to execute. Intended for INSERT, UPDATE, or DELETE statements (not SELECT).</td><td style="word-wrap: break-word;">INSERT INTO users (name, email) VALUES ('John Doe', 'john@example.com')</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable summary of the execution result, suitable for display or logging.</td><td style="word-wrap: break-word;">PostgreSQL Execute Results: 1 row affected.</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON response from the execution service, typically including status and rows affected.</td><td style="word-wrap: break-word;">{"status": "success", "rows_affected": 1}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **DML only**: This node is designed for INSERT, UPDATE, and DELETE. Use the corresponding query node for SELECT statements.
- **Credentials required**: Ensure the credentials at credentials_path are valid and have appropriate permissions for the target tables.
- **SQL safety**: Avoid constructing SQL from untrusted inputs to prevent SQL injection. Prefer parameterized statements where possible.
- **Timeout behavior**: Long-running statements may require increasing the timeout input to prevent premature failures.
- **Service-backed execution**: The node proxies execution through Salt's CData PostgreSQL service; network availability and service health can affect results.

## Troubleshooting
- **Syntax error in SQL**: Validate your SQL with a psql client or linter and correct any syntax issues.
- **Permission denied**: Verify the database user in credentials has privileges for the target schema/table and the specific operation.
- **Connection or service error**: Check network access to the service, confirm the credentials_path is correct, and retry. Increase timeout if needed.
- **0 rows affected**: Confirm your WHERE clause or target data is correct; the command may have executed successfully but matched no rows.
- **Statement times out**: Increase the timeout input or optimize the statement (e.g., add indexes) to reduce execution time.
