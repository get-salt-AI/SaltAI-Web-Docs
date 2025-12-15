# PostgreSQL Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes a PostgreSQL SELECT query using provided database credentials. Returns a human-readable summary along with the raw JSON result data. Designed for read-only operations; mutations should use the corresponding execute node.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to retrieve data from a PostgreSQL database. Provide a credentials file path that matches the PostgreSQL credential template and a valid SQL SELECT statement. The node is typically placed early in a workflow to fetch data that subsequent nodes will analyze, transform, or visualize.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored PostgreSQL credentials file that follows the 'postgres' credential template (e.g., host, port, database, username, password).</td><td style="word-wrap: break-word;">/path/to/credentials/postgres.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the query to complete before timing out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL SELECT statement to execute against the PostgreSQL database.</td><td style="word-wrap: break-word;">SELECT id, name, email FROM users LIMIT 10</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A short, human-readable summary title for the query result.</td><td style="word-wrap: break-word;">PostgreSQL Query Results</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON data returned by the query for downstream processing.</td><td style="word-wrap: break-word;">[{'id': 1, 'name': 'John Doe', 'email': 'john@example.com'}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is intended for read-only operations (SELECT). For INSERT/UPDATE/DELETE, use SaltPostgresExecute.
- Ensure the credentials file matches the 'postgres' credential template and contains valid connection details.
- Large result sets may impact performance and could be truncated upstream or cause timeouts; consider adding LIMIT or filtering.
- SQL must be valid for PostgreSQL dialect; parameterization is not supported in this node's interface.
- Timeout applies to the entire request cycle; increase it for long-running queries.

## Troubleshooting
- Query fails with syntax error: Verify the SQL is valid PostgreSQL syntax and test it directly in a SQL client.
- Authentication/connection error: Confirm credentials_path points to a valid file and that host/port/database/user/password are correct and reachable.
- Request timed out: Increase the timeout value or optimize the query by adding indexes/filters/LIMIT.
- Empty or unexpected results: Check the target schema/table and WHERE clauses; ensure the connected user has SELECT permissions.
- Invalid credentials file format: Make sure the credentials JSON follows the expected 'postgres' template keys and structure.
