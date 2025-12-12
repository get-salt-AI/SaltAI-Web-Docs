# MySQL Visual Query Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds and executes MySQL SELECT queries using a visual-style configuration instead of writing SQL. Supports selecting columns, multiple JOINs (INNER/LEFT/RIGHT/FULL/CROSS), WHERE, GROUP BY, HAVING, ORDER BY, LIMIT and OFFSET. Can return results as text/JSON or export as HTML, Excel (XLSX), PDF, or all formats.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlvisualquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to compose complex MySQL queries without hand-writing SQL. Typical workflow: first discover tables/columns with helper nodes, then configure main_table, selected_columns, and optional join_config/filters here. Choose an output_format depending on whether you want text/JSON for downstream logic or an export (HTML/XLSX/PDF) for sharing.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored MySQL credentials configured for this service.</td><td style="word-wrap: break-word;"><path-to-mysql-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the request to complete.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">main_table</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary table to query from.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database/schema name containing the main table.</td><td style="word-wrap: break-word;">sales_db</td></tr>
<tr><td style="word-wrap: break-word;">selected_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Columns to select. Use '*' or comma-separated list; supports qualified names and aliases.</td><td style="word-wrap: break-word;">users.id, users.name, orders.total_amount</td></tr>
<tr><td style="word-wrap: break-word;">join_config</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array describing JOINs. Each item: {"type": "INNER\|LEFT\|RIGHT\|FULL\|CROSS", "table": "...", "database": "..." (optional), "on": "join condition"}.</td><td style="word-wrap: break-word;">[{"type":"LEFT","table":"orders","database":"sales_db","on":"users.id = orders.user_id"}]</td></tr>
<tr><td style="word-wrap: break-word;">where_conditions</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional WHERE clause conditions (without the WHERE keyword).</td><td style="word-wrap: break-word;">users.status = 'active' AND orders.created_at >= '2024-01-01'</td></tr>
<tr><td style="word-wrap: break-word;">group_by_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional GROUP BY columns (comma-separated, without the GROUP BY keyword).</td><td style="word-wrap: break-word;">users.id, users.country</td></tr>
<tr><td style="word-wrap: break-word;">having_conditions</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional HAVING clause conditions (without the HAVING keyword).</td><td style="word-wrap: break-word;">COUNT(orders.id) > 2</td></tr>
<tr><td style="word-wrap: break-word;">order_by_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional ORDER BY clause (without the ORDER BY keyword). Use DESC for descending.</td><td style="word-wrap: break-word;">users.created_at DESC, users.id ASC</td></tr>
<tr><td style="word-wrap: break-word;">limit_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of rows to return. Must be >= 1.</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">offset_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of rows to skip before returning results. Must be >= 0.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">['text', 'html', 'xlsx', 'pdf', 'all']</td><td style="word-wrap: break-word;">Select the output format: text (text + JSON), html (HTML table), xlsx (Excel), pdf (PDF), or all (all formats).</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the query results. Populated for text and all output modes.</td><td style="word-wrap: break-word;">Visual Query Results (100 rows): ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON of the query results, including rows and counts. Always populated.</td><td style="word-wrap: break-word;">{"data": [{"id": 1, "name": "Alice"}], "row_count": 1}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML table representation of the results. Populated when output_format is html or all.</td><td style="word-wrap: break-word;"><table><thead>...</thead><tbody>...</tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Excel workbook bytes for the results. Populated when output_format is xlsx or all.</td><td style="word-wrap: break-word;"><binary-xlsx-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF bytes rendering of the results. Populated when output_format is pdf or all.</td><td style="word-wrap: break-word;"><binary-pdf-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Join configuration must be valid JSON and a list of objects. Required fields per join: type, table, on.
- Supported join types: INNER, LEFT, RIGHT, FULL, CROSS. CROSS joins do not use an ON clause.
- The node constructs SQL from your inputs; ensure identifiers and conditions are valid for your database.
- Limit and offset are appended only when greater than zero.
- When output_format is text, the node returns a formatted text summary along with JSON; other export fields will be empty.
- For html/xlsx/pdf formats, only the selected format (and JSON) is returned; use 'all' to get every format.
- Credentials referenced by credentials_path must have permission to access the specified database and tables.

## Troubleshooting
- Invalid JSON in join_config: Ensure join_config is a valid JSON array string and includes required fields. Example: [{"type":"INNER","table":"orders","on":"users.id = orders.user_id"}].
- Invalid join type error: Use only INNER, LEFT, RIGHT, FULL, or CROSS.
- Empty results: Check WHERE conditions, joins, and limit/offset values; try removing filters or increasing limit_count.
- Permission or access errors: Verify the credentials have SELECT privileges on all referenced tables and schemas.
- Timeouts: Increase the timeout input if queries are long-running or optimize the query (indexes, narrower column selection).
- Syntax errors: Review where_conditions, group_by, having, and order_by inputs for SQL correctness; do not include the keywords (WHERE/GROUP BY/HAVING/ORDER BY) themselves.
