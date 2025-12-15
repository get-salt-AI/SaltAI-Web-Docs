# SQL Server Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a Microsoft SQL Server SELECT query using configured credentials. It loads the connection details from a credentials file and executes the SQL text, returning both a human-readable summary and machine-readable data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqlquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to read data from a SQL Server database as part of a data retrieval or analytics workflow. Provide a valid credentials file for the target SQL Server instance and a SELECT statement. Chain the outputs to downstream nodes that need tabular data or JSON for further processing.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored SQL Server credentials file that contains host, port, database, username, and password.</td><td style="word-wrap: break-word;">/workspace/secrets/mssql.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the query to complete before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL SELECT statement to execute against SQL Server. Multiline input supported.</td><td style="word-wrap: break-word;">SELECT TOP 10 id, name, email FROM dbo.users ORDER BY created_at DESC</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A readable summary of the query results, suitable for logs or quick inspection.</td><td style="word-wrap: break-word;">SQL Server Query Results: 10 rows returned from dbo.users</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">The raw query result data as JSON for programmatic use downstream.</td><td style="word-wrap: break-word;">[{"id": 1, "name": "Alice", "email": "alice@example.com"}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Read-only focus**: This node is intended for SELECT queries. Use SaltMSSQLExecute for INSERT/UPDATE/DELETE.
- **Credentials required**: The credentials file must be valid and have permissions to run the provided query.
- **Timeouts**: Long-running queries may exceed the timeout and fail; adjust the timeout or optimize the query.
- **Result size**: Very large result sets can be slow and memory intensive; consider limiting rows with TOP or filters.
- **Security**: Avoid untrusted dynamic input in SQL to prevent SQL injection.

## Troubleshooting
- **Authentication failed**: Verify credentials_path points to a valid file and that username/password and database are correct.
- **Connection timeout**: Increase the timeout input or ensure the SQL Server host, port, and network access are correct.
- **Syntax error in SQL**: Validate the sql_text against SQL Server syntax (e.g., use TOP for limits).
- **Empty or unexpected results**: Confirm table/schema names (e.g., dbo.users) and add appropriate WHERE filters.
- **Insufficient permissions**: Ensure the database user has SELECT privileges on the referenced tables or views.
