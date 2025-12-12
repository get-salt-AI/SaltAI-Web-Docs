# PostgreSQL Visual Query Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds and executes PostgreSQL SELECT queries from a visual-style configuration without writing SQL. Supports selecting columns, multiple JOIN types, WHERE, GROUP BY, HAVING, ORDER BY, LIMIT/OFFSET, and exporting results in multiple formats. Designed to help non-SQL users construct complex queries reliably.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresvisualquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to query PostgreSQL data by describing tables, joins, and filters rather than writing raw SQL. Start by setting the main table and schema, choose columns, optionally add JOINs via a JSON config, and refine results with conditions and grouping. Choose an output format (text, HTML, XLSX, PDF, or all) to integrate with downstream steps such as reporting, dashboards, or data pipelines.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to saved PostgreSQL connection credentials using the 'postgres' template.</td><td style="word-wrap: break-word;">/workspace/credentials/postgres.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the database operation to complete.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">main_table</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the primary table to query from.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">main_table_schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema of the primary table.</td><td style="word-wrap: break-word;">public</td></tr>
<tr><td style="word-wrap: break-word;">selected_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of columns to select, or '*' to select all columns. Supports fully qualified names when needed.</td><td style="word-wrap: break-word;">users.id, users.name, orders.total_amount</td></tr>
<tr><td style="word-wrap: break-word;">join_config</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array describing JOINs to apply. Each item should include type, table, on, and optional schema.</td><td style="word-wrap: break-word;">[{"type":"INNER","table":"orders","schema":"public","on":"users.id = orders.user_id"},{"type":"LEFT","table":"payments","schema":"public","on":"orders.id = payments.order_id"}]</td></tr>
<tr><td style="word-wrap: break-word;">where_conditions</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional WHERE clause conditions (without the 'WHERE' keyword).</td><td style="word-wrap: break-word;">users.status = 'active' AND orders.created_at >= '2024-01-01'</td></tr>
<tr><td style="word-wrap: break-word;">group_by_columns</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of columns for GROUP BY.</td><td style="word-wrap: break-word;">users.id, users.name</td></tr>
<tr><td style="word-wrap: break-word;">having_conditions</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional HAVING clause for aggregated results (without the 'HAVING' keyword).</td><td style="word-wrap: break-word;">COUNT(orders.id) > 3</td></tr>
<tr><td style="word-wrap: break-word;">order_by_columns</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of columns for ORDER BY. Append DESC for descending order.</td><td style="word-wrap: break-word;">users.created_at DESC, users.id ASC</td></tr>
<tr><td style="word-wrap: break-word;">limit_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of rows to return.</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">offset_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of rows to skip before starting to return rows.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Choose output format: 'text' (plain text plus JSON), 'html' (HTML table), 'xlsx' (Excel), 'pdf' (PDF), or 'all' (includes all exportable formats).</td><td style="word-wrap: break-word;">all</td></tr>
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
<tr><td style="word-wrap: break-word;">text_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary or formatted results when using text output.</td><td style="word-wrap: break-word;">Visual Query: public.users Visual Query Results (100 rows): ...</td></tr>
<tr><td style="word-wrap: break-word;">json_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the query result set and metadata for programmatic use.</td><td style="word-wrap: break-word;">{"data":[{"id":1,"name":"Alice"}],"row_count":1}</td></tr>
<tr><td style="word-wrap: break-word;">html_output</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML table representation of the results when requested.</td><td style="word-wrap: break-word;"><table><thead><tr><th>id</th><th>name</th></tr></thead><tbody><tr><td>1</td><td>Alice</td></tr></tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_file</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Excel file bytes containing the results when requested.</td><td style="word-wrap: break-word;"><binary-xlsx-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf_file</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF file bytes containing the results when requested.</td><td style="word-wrap: break-word;"><binary-pdf-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **JOIN configuration format**: join_config must be a valid JSON array. Each item must include 'type' (INNER, LEFT, RIGHT, FULL, or CROSS), 'table', and 'on'. 'schema' is optional and defaults to 'public'.
- **Where/Having syntax**: Provide only the condition expressions (do not include the keywords WHERE or HAVING).
- **Column selection**: Use '*' to select all columns or comma-separated columns. You can qualify columns with schema.table.column to avoid ambiguity.
- **Limits and pagination**: Use limit_count and offset_count to control result size and paging.
- **Export behavior**: output_format controls which of the outputs are populated. 'text' returns text and JSON; 'html' returns JSON and HTML; 'xlsx' returns JSON and XLSX; 'pdf' returns JSON and PDF; 'all' returns text, JSON, and exportable formats when available.
- **Schemas**: If a schema isn’t provided for JOINs, 'public' is assumed.
- **Credentials**: Ensure the credentials_path points to a valid PostgreSQL connection configuration using the 'postgres' template.

## Troubleshooting
- **Invalid join_config JSON**: If you see a parsing error, ensure join_config is valid JSON (use double quotes for keys/strings) and structured as a list of objects.
- **Unsupported JOIN type**: Error about join type means the 'type' must be one of INNER, LEFT, RIGHT, FULL, or CROSS.
- **Empty results**: If no data is returned, verify WHERE/HAVING conditions, table names, and that the selected_columns exist.
- **Ambiguous columns**: If you get ambiguity errors, qualify columns with table or schema prefixes (e.g., users.id).
- **Permission errors**: If access is denied, confirm the connected user has SELECT privileges on all referenced tables and schemas.
- **Timeouts**: Increase the timeout or refine filters if complex queries against large datasets are timing out.
- **Large exports**: Very large result sets may produce large XLSX/PDF files; consider filtering, limiting rows, or using pagination.
