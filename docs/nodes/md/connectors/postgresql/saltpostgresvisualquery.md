# PostgreSQL Visual Query Builder

Builds and runs PostgreSQL SELECT queries from a visual-style configuration, including multi-table JOINs, filtering, grouping, ordering, pagination, and multiple export formats. It constructs SQL from inputs, executes it against a configured PostgreSQL data service, and returns text, JSON, and optional HTML/XLSX/PDF outputs.

## Usage

Use this node when you want to query PostgreSQL data without writing SQL directly. Specify the main table and schema, the columns to select, optional JOINs in JSON, filters (WHERE), grouping (GROUP BY/HAVING), ordering, and pagination limits. Choose the desired output format for reporting or downstream processing. Commonly used in analytics flows, dashboard/report generation, and data exploration pipelines.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database connection URI used to authenticate and route requests to the PostgreSQL data service.</td><td style="word-wrap: break-word;">postgresql://username:password@db.host:5432/my_database?schema=public</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (seconds) to wait for the service request before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">main_table</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The primary table to query in the FROM clause.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">main_table_schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema of the primary table.</td><td style="word-wrap: break-word;">public</td></tr>
<tr><td style="word-wrap: break-word;">selected_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated column list to select, or * for all. Supports qualified names and expressions.</td><td style="word-wrap: break-word;">users.id, users.name, orders.total_amount</td></tr>
<tr><td style="word-wrap: break-word;">join_config</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array describing JOINs. Each item must include type, table, on, and optional schema. Valid types: INNER, LEFT, RIGHT, FULL, CROSS.</td><td style="word-wrap: break-word;">[{"type":"INNER","table":"orders","schema":"public","on":"users.id = orders.user_id"}]</td></tr>
<tr><td style="word-wrap: break-word;">where_conditions</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">WHERE clause conditions without the WHERE keyword.</td><td style="word-wrap: break-word;">users.age > 25 AND users.city = 'New York'</td></tr>
<tr><td style="word-wrap: break-word;">group_by_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated columns to group by.</td><td style="word-wrap: break-word;">users.city</td></tr>
<tr><td style="word-wrap: break-word;">having_conditions</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HAVING clause to filter grouped results, without the HAVING keyword.</td><td style="word-wrap: break-word;">COUNT(orders.id) > 5</td></tr>
<tr><td style="word-wrap: break-word;">order_by_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated ORDER BY expressions, with optional ASC/DESC.</td><td style="word-wrap: break-word;">users.created_at DESC, users.id ASC</td></tr>
<tr><td style="word-wrap: break-word;">limit_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of rows to return (1–10000).</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">offset_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of rows to skip from the start (0–10000).</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">One of: text \| html \| xlsx \| pdf \| all</td><td style="word-wrap: break-word;">Controls which outputs are populated. 'text' returns formatted text and JSON; 'html' returns an HTML table; 'xlsx' returns a base64 Excel file; 'pdf' returns a base64 PDF; 'all' returns all formats.</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable, formatted text of the query results or an error message.</td><td style="word-wrap: break-word;">Visual Query: public.users (100 rows): Row 1: id: 1  name: Alice  total_amount: 45.00 ...</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON string of the service response, typically containing a 'data' array and metadata.</td><td style="word-wrap: break-word;">{"data":[{"id":1,"name":"Alice","total_amount":45.0}],"row_count":100}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML document containing a styled table of results. Populated for 'html' and 'all' formats.</td><td style="word-wrap: break-word;"><!DOCTYPE html><html>...<table class="data-table">...</table>...</html></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX file containing the results. Populated for 'xlsx' and 'all' formats.</td><td style="word-wrap: break-word;"><base64-xlsx-data></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF document containing the results. Populated for 'pdf' and 'all' formats.</td><td style="word-wrap: break-word;"><base64-pdf-data></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Join configuration**: join_config must be valid JSON representing a list of joins. Each join requires 'type', 'table', and 'on'. 'schema' defaults to 'public' if omitted.
- **Valid JOIN types**: Only INNER, LEFT, RIGHT, FULL, and CROSS are allowed; any other type will cause an error.
- **Credentials**: credentials_path must be a valid database URI (e.g., postgresql://user:pass@host:port/db). The PostgreSQL data connector endpoint must be configured.
- **Output behavior**: Output fields are populated based on output_format. For 'text', only result and json_result are returned; other fields are empty strings.
- **Empty datasets**: HTML/XLSX/PDF exports may be empty if no data rows are returned.
- **Limits**: limit_count > 0 will apply LIMIT; offset_count > 0 applies OFFSET. Values are constrained to 1–10000 and 0–10000 respectively.
- **Security**: where_conditions, having_conditions, and order_by_columns are embedded directly into SQL; ensure they are trusted to avoid SQL injection.
- **Error handling**: On failure, result contains a descriptive message and json_result contains an error object; other outputs are empty.

## Troubleshooting
- **Invalid credentials URI**: If you see 'Invalid credentials URI' or 'Service URL not configured', provide a proper postgresql:// URI and ensure the PostgreSQL data connector endpoint is set.
- **Invalid join_config JSON**: A 'Invalid JSON in join configuration' error means the join_config is not valid JSON or not an array. Provide a JSON array string.
- **Missing join fields**: Errors like 'Join X missing required field' mean each join object must include 'type', 'table', and 'on'.
- **Unsupported JOIN type**: If you get 'Invalid join type', change 'type' to one of INNER, LEFT, RIGHT, FULL, or CROSS.
- **Empty results**: If result is empty, verify selected_columns, filters, and joins. Test a simpler query first (e.g., selected_columns='*', no joins) to confirm connectivity.
- **Timeouts**: Increase timeout if queries are long-running, or optimize filters/indexes.
- **Export files empty**: HTML/XLSX/PDF outputs are empty strings when no data rows are returned; confirm your query returns rows.
- **Syntax errors from inputs**: Ensure where_conditions, group_by_columns, having_conditions, and order_by_columns are valid SQL fragments without leading keywords.
