# PostgreSQL Visual Query Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds and executes PostgreSQL SELECT queries through a visual configuration, including multi-table JOINs. It generates SQL from inputs like selected columns, JOIN definitions, filters, grouping, ordering, and pagination, and can return results in text/JSON, HTML, XLSX, PDF, or all formats.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresvisualquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to construct complex PostgreSQL queries without writing SQL directly. It fits into workflows where you first supply a PostgreSQL connection URI, then configure the main table, joins, filters, and output format. The node executes the generated SQL against your database connector service and returns formatted results and optional exports for reporting or downstream processing.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database connection URI for PostgreSQL. This must be a valid database-style URI; it is parsed to route the request to the PostgreSQL data connector service.</td><td style="word-wrap: break-word;">postgresql://<username>:<password>@db.example.com:5432/mydb?schema=public</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Request timeout in seconds for the database service call.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">main_table</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary table to query from.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">main_table_schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema of the main table.</td><td style="word-wrap: break-word;">public</td></tr>
<tr><td style="word-wrap: break-word;">selected_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Columns to include in SELECT (comma-separated). Use '*' for all columns.</td><td style="word-wrap: break-word;">users.id, users.name, orders.total_amount</td></tr>
<tr><td style="word-wrap: break-word;">join_config</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array describing JOINs. Each join object must include: type (INNER, LEFT, RIGHT, FULL, CROSS), table, optional schema (defaults to 'public'), and on condition (not used for CROSS joins).</td><td style="word-wrap: break-word;">[{"type": "INNER", "table": "orders", "schema": "public", "on": "users.id = orders.user_id"}, {"type": "LEFT", "table": "payments", "schema": "public", "on": "orders.id = payments.order_id"}]</td></tr>
<tr><td style="word-wrap: break-word;">where_conditions</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw SQL WHERE clause conditions (without the 'WHERE' keyword). Leave empty for no filter.</td><td style="word-wrap: break-word;">users.signup_date >= '2024-01-01' AND users.status = 'active'</td></tr>
<tr><td style="word-wrap: break-word;">group_by_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Columns to GROUP BY (comma-separated). Leave empty for no grouping.</td><td style="word-wrap: break-word;">users.id, users.name</td></tr>
<tr><td style="word-wrap: break-word;">having_conditions</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw SQL HAVING clause conditions (without the 'HAVING' keyword); used with GROUP BY.</td><td style="word-wrap: break-word;">COUNT(orders.id) > 2</td></tr>
<tr><td style="word-wrap: break-word;">order_by_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Columns to ORDER BY (comma-separated). Append DESC for descending.</td><td style="word-wrap: break-word;">users.created_at DESC, users.id ASC</td></tr>
<tr><td style="word-wrap: break-word;">limit_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of rows to return. Must be >= 1.</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">offset_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of rows to skip before starting to return rows. Use 0 for no offset.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">["text", "html", "xlsx", "pdf", "all"]</td><td style="word-wrap: break-word;">Controls how results are returned. 'text' returns human-readable text plus JSON; 'html' returns an HTML table; 'xlsx' returns an Excel file as base64; 'pdf' returns a PDF as base64; 'all' returns all formats.</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of query results. When output_format is html/xlsx/pdf, this may be empty.</td><td style="word-wrap: break-word;">Visual Query: public.users Query Results (100 rows): users.id: 1 \| users.name: Alice \| orders.total_amount: 120.50 ...</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON response from the database service including rows and metadata.</td><td style="word-wrap: break-word;">{"data": [{"users.id": 1, "users.name": "Alice", "orders.total_amount": 120.5}], "row_count": 1}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML document containing a styled table of results. Returned when output_format is 'html' or 'all'. Empty for other formats.</td><td style="word-wrap: break-word;"><!DOCTYPE html>...<table class="data-table">...</table>...</td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX content of results. Returned when output_format is 'xlsx' or 'all'. Empty for other formats.</td><td style="word-wrap: break-word;"><base64-xlsx-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF content of results. Returned when output_format is 'pdf' or 'all'. Empty for other formats.</td><td style="word-wrap: break-word;"><base64-pdf-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials URI required**: credentials_path must be a valid database URI (e.g., postgresql://<username>:<password>@<host>:<port>/<database>?schema=public).
- **JOIN configuration JSON**: join_config must be a JSON array of objects with fields: type, table, on; schema defaults to 'public'. Valid join types: INNER, LEFT, RIGHT, FULL, CROSS (CROSS has no ON condition).
- **Direct SQL fragments**: where_conditions, group_by_columns, having_conditions, and order_by_columns are inserted as-is into the SQL. Ensure they are syntactically correct and safe.
- **Row limits**: limit_count must be >= 1; offset_count must be >= 0.
- **Output behavior**: output_format controls which outputs are populated. 'text' returns only result and json_result; other formats return their respective fields, with others left empty. 'all' returns all formats.
- **Empty data handling**: If no rows are returned, export fields (HTML/XLSX/PDF) may be empty strings.
- **Service dependency**: The node forwards requests to the configured PostgreSQL data connector service endpoint; ensure endpoints are properly configured in the environment.

## Troubleshooting
- **Invalid credentials URI**: If you see errors about credentials or service URL, verify credentials_path is a properly formatted PostgreSQL URI and that the service endpoint is configured.
- **Invalid join_config JSON**: Errors like 'Invalid JSON' or 'Join configuration must be a list' indicate malformed JSON. Provide a valid JSON array of join objects.
- **Missing join fields**: Errors such as 'missing required field: type/table/on' mean one of these keys is absent in a join object.
- **Unsupported join type**: If you receive 'Invalid join type', change the type to one of: INNER, LEFT, RIGHT, FULL, CROSS.
- **SQL syntax errors**: If the service reports query errors, check selected_columns, where_conditions, group_by_columns, having_conditions, and order_by_columns for correctness.
- **No data returned**: If the result is empty, confirm your filters and joins are correct and that the tables contain data.
- **Timeouts**: Increase the timeout input if large queries or slow networks cause timeouts.
- **Empty exports**: HTML/XLSX/PDF fields can be empty when the result set is empty. Verify data or choose 'text' to inspect the JSON.
- **PDF/XLSX cannot open**: Ensure you decode the base64 string before saving to a file with the correct extension.
