# SQL Server Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes a read-only SQL SELECT query against a Microsoft SQL Server database using provided credentials. Accepts multiline SQL text and returns results in a structured format suitable for downstream processing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqlquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to retrieve data from SQL Server as part of a workflow. Provide a credentials file/path for a SQL Server connection and a SELECT statement. For INSERT/UPDATE/DELETE operations, use the dedicated execute node instead.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">CREDENTIALS</td><td style="word-wrap: break-word;">Path or reference to stored SQL Server credentials configured for the 'mssql' service.</td><td style="word-wrap: break-word;"><path-to-mssql-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the query to complete before aborting.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL SELECT query to execute. Multiline input is supported.</td><td style="word-wrap: break-word;">SELECT TOP 10 id, name, email FROM dbo.users ORDER BY created_at DESC;</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary or plain-text rendering of the query results.</td><td style="word-wrap: break-word;">Returned 10 rows from dbo.users</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured JSON payload of the query results for programmatic use.</td><td style="word-wrap: break-word;">{"rows": [{"id":1,"name":"John"}], "row_count": 10}</td></tr>
<tr><td style="word-wrap: break-word;">table</td><td style="word-wrap: break-word;">TABLE</td><td style="word-wrap: break-word;">Tabular representation of the result set suitable for table-aware nodes.</td><td style="word-wrap: break-word;">Not specified</td></tr>
<tr><td style="word-wrap: break-word;">columns</td><td style="word-wrap: break-word;">LIST</td><td style="word-wrap: break-word;">List of column names/types returned by the query.</td><td style="word-wrap: break-word;">["id", "name", "email", "created_at"]</td></tr>
<tr><td style="word-wrap: break-word;">rows</td><td style="word-wrap: break-word;">LIST</td><td style="word-wrap: break-word;">List of row values corresponding to the columns.</td><td style="word-wrap: break-word;">[[1, "John", "john@example.com", "2024-01-01T00:00:00Z"]]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Read-only**: This node is intended for SELECT queries. Use 'SQL Server Execute' for INSERT/UPDATE/DELETE.
- **Credentials**: Requires a valid SQL Server credential set configured for the 'mssql' service.
- **Timeouts**: Long-running queries may time out; adjust the timeout input as needed.
- **Schema qualification**: Specify schema names (e.g., dbo.table_name) to avoid ambiguity.
- **Security**: Avoid interpolating untrusted input into sql_text to prevent SQL injection.

## Troubleshooting
- **Connection errors**: Verify credentials, host, port, and network/firewall access if the node cannot connect.
- **Authentication failures**: Ensure username/password or integrated security settings in the credentials are correct.
- **Query syntax errors**: Validate the SQL syntax against SQL Server dialect; test the query in a SQL client.
- **Timeouts**: Increase the timeout input or optimize the query/indexes if queries run too long.
- **Permission denied**: Ensure the database user has SELECT permissions on referenced schemas and tables.
