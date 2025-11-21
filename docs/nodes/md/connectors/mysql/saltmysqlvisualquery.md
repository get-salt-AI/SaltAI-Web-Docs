# MySQL Visual Query Builder

Builds and runs MySQL SELECT queries from a visual-style configuration, including multi-table JOINs, filters, grouping, ordering, and pagination. It can return results as plain text with JSON, or export them as HTML, XLSX, and PDF tables.

## Usage

Use this node when you want to query a MySQL database without writing SQL directly. Provide a credentials URI and configure the main table, selected columns, JOINs, filters, grouping, and sorting. Choose an output format to get human-readable text, HTML for viewing, or XLSX/PDF for sharing. Typically used after establishing access to a MySQL database and before downstream processing or reporting.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database connection URI used to authenticate and route the request.</td><td style="word-wrap: break-word;">mysql://username:<password>@db.example.com:3306/my_database</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Request timeout in seconds for the query execution.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">main_table</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary table to select from.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database name containing the main table.</td><td style="word-wrap: break-word;">sales_db</td></tr>
<tr><td style="word-wrap: break-word;">selected_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated column list to select, or * for all columns.</td><td style="word-wrap: break-word;">users.id, users.name, orders.total</td></tr>
<tr><td style="word-wrap: break-word;">join_config</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array describing JOINs. Each item requires 'type', 'table', and 'on'. Optional 'database' defaults to 'mysql'.</td><td style="word-wrap: break-word;">[{"type": "INNER", "table": "orders", "database": "sales_db", "on": "users.id = orders.user_id"}]</td></tr>
<tr><td style="word-wrap: break-word;">where_conditions</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL WHERE conditions (omit the WHERE keyword).</td><td style="word-wrap: break-word;">users.active = 1 AND users.signup_date >= '2024-01-01'</td></tr>
<tr><td style="word-wrap: break-word;">group_by_columns</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated column list for GROUP BY.</td><td style="word-wrap: break-word;">users.country</td></tr>
<tr><td style="word-wrap: break-word;">having_conditions</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL HAVING conditions (omit the HAVING keyword).</td><td style="word-wrap: break-word;">COUNT(orders.id) > 5</td></tr>
<tr><td style="word-wrap: break-word;">order_by_columns</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated ORDER BY clauses. Add DESC for descending.</td><td style="word-wrap: break-word;">users.signup_date DESC, users.id ASC</td></tr>
<tr><td style="word-wrap: break-word;">limit_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of rows to return.</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">offset_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of rows to skip (for pagination).</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">text \| html \| xlsx \| pdf \| all</td><td style="word-wrap: break-word;">Choose text (plain text + JSON), html (HTML table), xlsx (Excel as base64), pdf (PDF as base64), or all (returns all available formats).</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the query results (plain text).</td><td style="word-wrap: break-word;">Visual Query Results (100 rows): Row 1: id: 1 \| name: Alice \| total: 125.00 ...</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw result payload serialized as a JSON string, typically including rows and row count.</td><td style="word-wrap: break-word;">{"data": [{"id": 1, "name": "Alice", "total": 125.0}], "row_count": 1}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML table representation of the results when output_format is html or all. Empty for text-only output.</td><td style="word-wrap: break-word;"><!DOCTYPE html><html>...<table><thead>...</thead><tbody>...</tbody></table>...</html></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX data when output_format is xlsx or all. Empty for other formats.</td><td style="word-wrap: break-word;"><base64-xlsx-string></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF data when output_format is pdf or all. Empty for other formats.</td><td style="word-wrap: break-word;"><base64-pdf-string></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials URI required**: Provide a valid MySQL-style URI in credentials_path (e.g., mysql://username:<password>@host:port/db).
- **JOIN config format**: join_config must be valid JSON array. Each item must include 'type', 'table', and 'on'. Optional 'database' defaults to 'mysql'.
- **Allowed JOIN types**: INNER, LEFT, RIGHT, FULL, CROSS. CROSS JOIN ignores 'on'.
- **SQL fragments only**: where_conditions, having_conditions, order_by_columns should be SQL fragments without leading keywords (WHERE, HAVING, ORDER BY).
- **Pagination**: limit_count and offset_count translate to LIMIT and OFFSET in the final query.
- **Output formats**: 'text' returns text and JSON; 'html' returns HTML + JSON; 'xlsx' returns XLSX(base64) + JSON; 'pdf' returns PDF(base64) + JSON; 'all' returns all formats.

## Troubleshooting
- **Invalid credentials URI**: Ensure credentials_path starts with mysql:// and includes host, database, and valid placeholders for auth.
- **Malformed join_config**: If parsing fails, validate that join_config is valid JSON and an array of objects with 'type', 'table', and 'on'.
- **Unsupported JOIN type**: Use only INNER, LEFT, RIGHT, FULL, or CROSS.
- **Empty results**: If no rows are returned, check filters, LIMIT/OFFSET, and selected_columns.
- **Export fields are empty**: Ensure output_format matches the expected export; e.g., choose html for HTML table or xlsx/pdf for file exports.
- **SQL errors**: Validate column names, table names, and SQL fragments in where_conditions/having_conditions/order_by_columns.
