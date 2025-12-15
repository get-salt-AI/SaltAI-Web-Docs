# PostgreSQL Visual Query Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds and executes PostgreSQL SELECT queries using a visual, parameter-driven configuration. Supports JOINs (INNER, LEFT, RIGHT, FULL, CROSS), WHERE/HAVING, GROUP BY, ORDER BY, LIMIT/OFFSET, and multiple output formats. Ideal for constructing complex queries without writing raw SQL.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresvisualquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to query a PostgreSQL database by configuring tables, joins, and filters through parameters. Provide a PostgreSQL connection URI, specify the main table and schema, define columns to select, and optionally add joins and conditions. Choose the desired output format: plain text (with JSON), HTML table, XLSX, PDF, or all formats at once.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database URI containing connection details for PostgreSQL.</td><td style="word-wrap: break-word;">postgresql://<username>:<password>@<host>:5432/<database>?ssl_mode=prefer</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Request timeout in seconds for the database service call.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">main_table</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the main table to query.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">main_table_schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema name of the main table.</td><td style="word-wrap: break-word;">public</td></tr>
<tr><td style="word-wrap: break-word;">selected_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of columns to select, or * for all columns.</td><td style="word-wrap: break-word;">users.id, users.name, orders.total_amount</td></tr>
<tr><td style="word-wrap: break-word;">join_config</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array describing JOINs. Each item must include type, table, and on; schema is optional (defaults to public).</td><td style="word-wrap: break-word;">[{"type": "INNER", "table": "orders", "schema": "public", "on": "users.id = orders.user_id"}]</td></tr>
<tr><td style="word-wrap: break-word;">where_conditions</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL WHERE clause conditions to filter rows.</td><td style="word-wrap: break-word;">users.age > 25 AND users.city = 'New York'</td></tr>
<tr><td style="word-wrap: break-word;">group_by_columns</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of columns for GROUP BY.</td><td style="word-wrap: break-word;">users.city, users.age</td></tr>
<tr><td style="word-wrap: break-word;">having_conditions</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL HAVING clause applied after GROUP BY.</td><td style="word-wrap: break-word;">COUNT(users.id) > 5</td></tr>
<tr><td style="word-wrap: break-word;">order_by_columns</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of columns to order by. Append DESC for descending.</td><td style="word-wrap: break-word;">users.created_at DESC, users.id</td></tr>
<tr><td style="word-wrap: break-word;">limit_count</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of rows to return. Must be >= 1 to apply.</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">offset_count</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of rows to skip before starting to return rows.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">["text", "html", "xlsx", "pdf", "all"]</td><td style="word-wrap: break-word;">Output format for results. 'text' returns formatted text plus JSON; 'html' returns an HTML table; 'xlsx' returns an Excel file (base64); 'pdf' returns a PDF (base64); 'all' returns all formats.</td><td style="word-wrap: break-word;">all</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable, formatted text summary of the query results.</td><td style="word-wrap: break-word;">Visual Query Results (10 rows): ...</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw query results serialized as JSON string.</td><td style="word-wrap: break-word;">{"data": [{"id": 1, "name": "Alice"}], "row_count": 10}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML string representing the result set as a table. Empty if not requested.</td><td style="word-wrap: break-word;"><!DOCTYPE html>...<table>...</table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX content of the results. Empty if not requested.</td><td style="word-wrap: break-word;"><base64-xlsx-data></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF content of the results. Empty if not requested.</td><td style="word-wrap: break-word;"><base64-pdf-data></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Join configuration must be valid JSON and structured as a list of objects with keys: type, table, on; schema is optional and defaults to public.
- Valid JOIN types are: INNER, LEFT, RIGHT, FULL, CROSS. CROSS JOIN does not use an ON condition.
- selected_columns accepts '*' or a comma-separated list; qualify columns with table names to avoid ambiguity (e.g., users.id).
- where_conditions and having_conditions are inserted into the SQL as provided; ensure they are syntactically valid and safe.
- limit_count and offset_count are applied only if greater than zero.
- When output_format is 'all', all output fields are populated; for other formats, only json_result plus the requested format output are returned.
- xlsx_data and pdf_data are returned as base64-encoded strings; decode before saving to files.
- The node requires a valid PostgreSQL URI in credentials_path; unsupported or malformed URIs will cause validation errors.

## Troubleshooting
- Invalid JSON in join_config: Ensure join_config is a JSON array string. Example: [{"type": "INNER", "table": "orders", "on": "users.id = orders.user_id"}].
- Invalid JOIN type error: Use one of INNER, LEFT, RIGHT, FULL, CROSS.
- No data returned: Verify WHERE/HAVING filters and that selected_columns and joins reference existing columns.
- Credentials error: Provide a correct PostgreSQL URI (e.g., postgresql://<username>:<password>@<host>:5432/<database>).
- Service URL not configured: Ensure the PostgreSQL data connector endpoint is available in the environment configuration.
- Timeouts: Increase the timeout input for long-running queries or optimize the query (indexes, filters).
- Export outputs empty: html_table/xlsx_data/pdf_data are only populated when output_format is html/xlsx/pdf/all respectively.
- SQL syntax errors: Check that where_conditions, group_by_columns, having_conditions, and order_by_columns are valid SQL for PostgreSQL.
