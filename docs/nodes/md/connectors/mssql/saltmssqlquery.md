# SQL Server Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes a SQL SELECT query against a Microsoft SQL Server database using provided credentials. Loads credentials from a path, sends the query to the SQL Server service, and returns results in multiple formats for easy downstream use.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqlquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to read data from an MSSQL database. Provide a credentials file/path for the 'mssql' service and a valid SELECT statement. This node is typically used before data processing, analysis, or export steps. For INSERT/UPDATE/DELETE, use the corresponding Execute node instead.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to stored database credentials configured for the 'mssql' service. The credentials must follow the expected template.</td><td style="word-wrap: break-word;">/path/to/credentials/mssql.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the query to complete before timing out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL SELECT query to execute against SQL Server.</td><td style="word-wrap: break-word;">SELECT TOP 10 id, name, email FROM dbo.users ORDER BY created_at DESC</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary or formatted text of the query results.</td><td style="word-wrap: break-word;">SQL Server Query Results (10 rows) Row 1: id=1, name=..., ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw query results encoded as JSON for programmatic consumption.</td><td style="word-wrap: break-word;">{"data": [{"id": 1, "name": "Alice"}], "row_count": 10}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML table representation of the results, suitable for rendering in UIs.</td><td style="word-wrap: break-word;"><table><thead><tr><th>id</th><th>name</th></tr></thead><tbody><tr><td>1</td><td>Alice</td></tr></tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">An Excel (.xlsx) binary payload containing the query results.</td><td style="word-wrap: break-word;"><bytes of XLSX file></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">A PDF binary payload with a table of the query results.</td><td style="word-wrap: break-word;"><bytes of PDF file></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Only use this node for read-only SELECT queries. For INSERT/UPDATE/DELETE, use the 'SQL Server Execute' node.
- Credentials must match the 'mssql' credential template and be valid for the target server.
- Large result sets may impact performance and memory; consider limiting rows with TOP or filtering with WHERE.
- Timeout applies to the entire request; increase it for long-running queries if necessary.
- The output formats may return empty values when not applicable to the operation or configuration.

## Troubleshooting
- Authentication failed or login error: Verify the credentials file at credentials_path uses the correct 'mssql' template and contains correct host, database, username, and password.
- Connection/timeout errors: Check network access to the SQL Server host and port, firewall rules, and increase the timeout if queries are long-running.
- SQL syntax errors: Ensure sql_text is a valid SQL Server SELECT statement and references the correct schema (e.g., dbo.table).
- Insufficient permissions: Confirm the database user has SELECT privileges on the targeted tables and schemas.
- Empty or partial results: Validate filtering conditions, confirm the table contains data, and reduce overly restrictive WHERE clauses.
