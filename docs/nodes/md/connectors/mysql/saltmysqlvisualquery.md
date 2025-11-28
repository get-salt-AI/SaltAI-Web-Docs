# MySQL Visual Query Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds and executes MySQL SELECT queries using a visual-style configuration instead of raw SQL. Supports JOINs (INNER, LEFT, RIGHT, FULL, CROSS), WHERE, GROUP BY, HAVING, ORDER BY, LIMIT, and OFFSET. Can output results in multiple formats including plain text/JSON, HTML, XLSX, and PDF.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlvisualquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to query MySQL data without hand-writing SQL, or when you need to construct complex multi-table queries with joins in a structured way. Typical workflow: provide credentials, choose the main table and database, specify selected columns and optional joins/filters/ordering, then pick the desired output format for downstream reporting or export.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reference/path to stored MySQL credentials to authenticate requests.</td><td style="word-wrap: break-word;">secrets/mysql-prod.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the query to execute before failing.</td><td style="word-wrap: break-word;">120</td></tr>
<tr><td style="word-wrap: break-word;">main_table</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary table to query from.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database name that contains the main table.</td><td style="word-wrap: break-word;">salesdb</td></tr>
<tr><td style="word-wrap: break-word;">selected_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Columns to include in the SELECT clause. Use comma-separated columns or '*' for all columns. Supports qualified names (e.g., users.id, orders.total).</td><td style="word-wrap: break-word;">users.id, users.name, orders.total</td></tr>
<tr><td style="word-wrap: break-word;">join_config</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array (as a string) describing JOINs. Each item must include 'type', 'table', and 'on'. Optional 'database' defaults to 'mysql' if omitted.</td><td style="word-wrap: break-word;">[{"type":"INNER","table":"orders","database":"salesdb","on":"users.id = orders.user_id"}]</td></tr>
<tr><td style="word-wrap: break-word;">where_conditions</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Text for the WHERE clause without the 'WHERE' keyword.</td><td style="word-wrap: break-word;">users.status = 'active' AND orders.total > 100</td></tr>
<tr><td style="word-wrap: break-word;">group_by_columns</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated columns for GROUP BY.</td><td style="word-wrap: break-word;">users.country</td></tr>
<tr><td style="word-wrap: break-word;">having_conditions</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Text for the HAVING clause without the 'HAVING' keyword (used with GROUP BY).</td><td style="word-wrap: break-word;">COUNT(orders.id) > 2</td></tr>
<tr><td style="word-wrap: break-word;">order_by_columns</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated ORDER BY columns. Append DESC for descending order if needed.</td><td style="word-wrap: break-word;">users.created_at DESC, users.id ASC</td></tr>
<tr><td style="word-wrap: break-word;">limit_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of rows to return. Must be >= 1.</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">offset_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of rows to skip before returning results. Must be >= 0.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Result output format. One of: text, html, xlsx, pdf, all.</td><td style="word-wrap: break-word;">all</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the query results. Populated when output_format is 'text' or 'all'.</td><td style="word-wrap: break-word;">Visual Query: salesdb.users — 100 rows returned</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw results as JSON (typically includes a 'data' array and optional metadata). Always populated on success.</td><td style="word-wrap: break-word;">{"data":[{"id":1,"name":"Alice","total":250.0}]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML table representation of the results. Populated when output_format is 'html' or 'all'.</td><td style="word-wrap: break-word;"><table><thead><tr><th>id</th><th>name</th><th>total</th></tr></thead><tbody><tr><td>1</td><td>Alice</td><td>250.0</td></tr></tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Excel workbook bytes of the results. Populated when output_format is 'xlsx' or 'all'.</td><td style="word-wrap: break-word;"><binary-xlsx-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF document bytes containing a tabular rendering of the results. Populated when output_format is 'pdf' or 'all'.</td><td style="word-wrap: break-word;"><binary-pdf-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **JOIN types**: Supported join types are INNER, LEFT, RIGHT, FULL, and CROSS. Any other value will be rejected.
- **Join configuration format**: join_config must be a JSON array string. Each join requires 'type', 'table', and 'on'. 'database' is optional and defaults to 'mysql'.
- **SQL assembly**: The node constructs a SQL query from your inputs. Ensure where_conditions, having_conditions, and order_by_columns are valid SQL fragments.
- **Limits and offsets**: LIMIT is included only if limit_count > 0; OFFSET only if offset_count > 0.
- **Output selection**: Choose 'all' to get text, JSON, HTML, XLSX, and PDF simultaneously. Other formats return only the requested artifact plus JSON.
- **Empty results**: If no rows are returned, the text output will indicate no data; JSON output will return an empty data array.
- **Security**: Avoid injecting sensitive values directly in conditions. Store secrets in credentials and do not include passwords in inputs.

## Troubleshooting
- **Invalid join_config JSON**: If parsing fails, ensure join_config is valid JSON (as a string) and is an array of objects. Example: "[{\"type\":\"INNER\",\"table\":\"orders\",\"on\":\"users.id = orders.user_id\"}]"
- **Unsupported join type**: Error indicating invalid join type means 'type' must be one of INNER, LEFT, RIGHT, FULL, CROSS.
- **SQL syntax errors**: If the database reports a syntax error, review where_conditions, having_conditions, order_by_columns, and selected_columns for mistakes or missing qualifiers.
- **Unknown columns/tables**: Ensure selected_columns, join 'table', and column references exist and are correctly qualified with table names when necessary.
- **No data returned**: Verify filters in where_conditions and having_conditions aren't over-restrictive; try removing filters or increasing limit_count.
- **Permission/connection errors**: Confirm credentials_path points to valid MySQL credentials with required permissions and that the timeout is sufficient.
- **Empty HTML/XLSX/PDF outputs**: These are only populated when output_format matches the format or is 'all'. Set output_format accordingly.
