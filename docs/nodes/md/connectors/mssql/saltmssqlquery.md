# SQL Server Query

Executes a SQL SELECT query against a Microsoft SQL Server database using configured credentials. Accepts multiline SQL text and returns the query results. Intended for read-only operations; use the execute variant for INSERT/UPDATE/DELETE.

## Usage

Use this node when you need to fetch data from a SQL Server database as part of a workflow. Provide a valid credentials reference and a SELECT statement. Typically chained before data processing, filtering, or visualization nodes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Path or reference to stored SQL Server credentials to authenticate the request.</td><td style="word-wrap: break-word;">/configs/mssql.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Maximum time the operation is allowed to run before it is aborted.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A SQL SELECT query to execute against SQL Server. Multiline is supported.</td><td style="word-wrap: break-word;">SELECT TOP 10 * FROM users</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Query results returned by the service. Typically includes a human-readable text summary and a structured payload of rows.</td><td style="word-wrap: break-word;">Returned 10 rows from users</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Read-only scope**: This node is designed for SELECT queries. Use SaltMSSQLExecute for INSERT/UPDATE/DELETE.
- **Credentials**: Must use a valid SQL Server credential configuration (credential template 'mssql').
- **Timeouts**: Long-running queries may be terminated by the timeout setting.
- **Result size**: Very large result sets may be truncated or impact performance; consider adding WHERE, ORDER BY, or TOP.
- **SQL safety**: Do not include untrusted input directly in SQL to avoid injection risks.

## Troubleshooting
- **Authentication failed**: Verify the credentials_path points to a valid SQL Server credential configuration and that permissions allow SELECT on the target tables.
- **Connection/Network errors**: Ensure the database host, port, and firewall/VPN settings allow connectivity from the runtime environment.
- **SQL syntax error**: Validate the query in a SQL client; ensure table/column names and schema (e.g., dbo) are correct.
- **Timeouts**: Increase the timeout input or optimize the query with indexes and filters.
- **Empty results**: Confirm the query conditions are correct and the target table contains data.
