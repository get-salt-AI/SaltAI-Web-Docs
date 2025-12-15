# MySQL Visual Query Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds and runs MySQL SELECT queries from a visual-style configuration, including JOINs, WHERE, GROUP BY, HAVING, ORDER BY, LIMIT and OFFSET. It can return results in multiple formats (text+JSON, HTML table, Excel, PDF, or all) without requiring users to write raw SQL.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlvisualquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to compose complex MySQL queries from structured inputs rather than writing SQL directly. Typical workflow: provide credentials, pick a main table and database, list the columns to return, optionally add JOIN definitions and filters, then pick the output format. Connect the outputs to viewers, exporters, or downstream processing nodes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored MySQL credentials that match the 'mysql' credential template.</td><td style="word-wrap: break-word;"><path-to-mysql-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the query to complete.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">main_table</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary table to query from.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database/schema name that contains the tables.</td><td style="word-wrap: break-word;">mysql</td></tr>
<tr><td style="word-wrap: break-word;">selected_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of columns to select. Use '*' to select all.</td><td style="word-wrap: break-word;">users.id, users.name, orders.total_amount</td></tr>
<tr><td style="word-wrap: break-word;">join_config</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array describing JOINs. Each item must include 'type' (INNER\|LEFT\|RIGHT\|FULL\|CROSS), 'table', optional 'database', and 'on' condition (ignored for CROSS).</td><td style="word-wrap: break-word;">[{"type":"INNER","table":"orders","database":"mysql","on":"users.id = orders.user_id"}]</td></tr>
<tr><td style="word-wrap: break-word;">where_conditions</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional WHERE clause conditions without the 'WHERE' keyword.</td><td style="word-wrap: break-word;">users.age > 25 AND users.city = "New York"</td></tr>
<tr><td style="word-wrap: break-word;">group_by_columns</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional comma-separated columns for GROUP BY.</td><td style="word-wrap: break-word;">users.city</td></tr>
<tr><td style="word-wrap: break-word;">having_conditions</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional HAVING clause to use with GROUP BY, without the 'HAVING' keyword.</td><td style="word-wrap: break-word;">COUNT(users.id) > 10</td></tr>
<tr><td style="word-wrap: break-word;">order_by_columns</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional comma-separated ORDER BY columns (append DESC for descending).</td><td style="word-wrap: break-word;">users.created_at DESC, users.id</td></tr>
<tr><td style="word-wrap: break-word;">limit_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of rows to return (must be >= 1).</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">offset_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of rows to skip before starting to return rows.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Format of the output. Options: text, html, xlsx, pdf, all.</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary or table-like plaintext of the results, including a title.</td><td style="word-wrap: break-word;">Visual Query: mysql.users Visual Query Results (100 rows): ================================================== Row 1:   id: 1   name: Alice   ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-serialized result payload (typically an object containing an array of rows and optional metadata).</td><td style="word-wrap: break-word;">{"data":[{"id":1,"name":"Alice"},{"id":2,"name":"Bob"}],"row_count":2}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML table rendering of the results (only when output_format is html or all).</td><td style="word-wrap: break-word;"><h3>Visual Query: mysql.users</h3><table><thead><tr><th>id</th><th>name</th></tr></thead><tbody>...</tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Excel workbook bytes representing the query results (only when output_format is xlsx or all).</td><td style="word-wrap: break-word;"><binary-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">PDF document bytes representing the query results (only when output_format is pdf or all).</td><td style="word-wrap: break-word;"><binary-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials**: You must provide a valid credentials_path for the 'mysql' service; the node loads credentials before building and executing the query.
- **JOIN validation**: Each JOIN entry must contain 'type', 'table', and 'on' (except CROSS JOIN which ignores 'on'); invalid types will cause errors.
- **SQL generation**: The node constructs a SELECT statement from inputs. WHERE/HAVING/ORDER BY must be valid SQL fragments (do not include the keywords twice).
- **Limits**: LIMIT and OFFSET are appended only when greater than 0 (OFFSET is added only if > 0).
- **Output formats**: Choosing 'text' returns text and JSON. 'html' returns JSON+HTML. 'xlsx' returns JSON+XLSX. 'pdf' returns JSON+PDF. 'all' returns all formats.
- **Empty inputs**: If selected_columns is empty or whitespace, '*' is used. If join_config is empty/whitespace, no JOINs are added.
- **Error behavior**: Invalid join_config JSON or unsupported JOIN type will raise an error and return an error message in the text/JSON outputs.

## Troubleshooting
- **Invalid JSON in join_config**: Ensure join_config is a JSON array. Example: [{"type":"INNER","table":"orders","database":"mysql","on":"users.id = orders.user_id"}].
- **Unknown JOIN type**: Use only INNER, LEFT, RIGHT, FULL, or CROSS for 'type'.
- **Missing JOIN fields**: Each join must include 'type', 'table', and 'on' (except CROSS). Add the missing fields to proceed.
- **SQL syntax errors**: Verify WHERE/HAVING/ORDER BY fragments are valid SQL for MySQL and match your schema.
- **No results**: Check filters (WHERE/HAVING), increase limit_count, or confirm that tables and columns exist in the specified database.
- **Timeouts**: Increase the timeout input for long-running queries or optimize filters and joins.
- **Wrong database or table**: Ensure 'database' and 'main_table' exist and credentials have access.
