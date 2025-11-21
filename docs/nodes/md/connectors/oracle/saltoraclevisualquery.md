# Oracle Visual Query Builder

Builds and executes Oracle SQL queries from visual parameters without requiring users to write SQL. Supports JOINs via a JSON join configuration, optional WHERE/GROUP BY/HAVING/ORDER BY clauses, and row limiting. Results can be returned as plain text/JSON, or exported to HTML, Excel (XLSX), PDF, or all formats.

## Usage

Use this node when you need to compose complex Oracle queries interactively. Provide the main table and optional schema, list the columns to select, and optionally add JOINs, filters, grouping, and ordering. Choose the desired output format to get human-readable tables (HTML/PDF), spreadsheet-friendly data (XLSX), or a standard text/JSON response. Typically placed after a credentials provider and before downstream nodes that consume text, JSON, or file outputs.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored Oracle credentials configuration that this node will use to authenticate.</td><td style="word-wrap: break-word;">/data/credentials/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the query to complete before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">main_table</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary table to query from.</td><td style="word-wrap: break-word;">employees</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Oracle schema name. Leave empty to use the current user's default schema.</td><td style="word-wrap: break-word;">HR</td></tr>
<tr><td style="word-wrap: break-word;">selected_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Columns to select. Use comma-separated column names or '*' for all columns.</td><td style="word-wrap: break-word;">employee_id, first_name, last_name, department_id</td></tr>
<tr><td style="word-wrap: break-word;">join_config</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array describing JOINs to apply. Each item supports 'type' (INNER/LEFT/RIGHT/FULL), 'table', optional 'schema', and 'on' join condition.</td><td style="word-wrap: break-word;">[{"type":"INNER","table":"departments","schema":"HR","on":"employees.department_id = departments.department_id"}]</td></tr>
<tr><td style="word-wrap: break-word;">where_conditions</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filter conditions for the WHERE clause (without the WHERE keyword).</td><td style="word-wrap: break-word;">salary > 50000 AND department_id = 10</td></tr>
<tr><td style="word-wrap: break-word;">group_by_columns</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated columns to GROUP BY.</td><td style="word-wrap: break-word;">department_id</td></tr>
<tr><td style="word-wrap: break-word;">having_conditions</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HAVING clause conditions to filter aggregated results (without the HAVING keyword).</td><td style="word-wrap: break-word;">COUNT(*) > 5</td></tr>
<tr><td style="word-wrap: break-word;">order_by_columns</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated ORDER BY expressions. Append DESC for descending order as needed.</td><td style="word-wrap: break-word;">salary DESC, last_name ASC</td></tr>
<tr><td style="word-wrap: break-word;">limit_rows</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Limit the number of rows returned (0 means no limit).</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">['text', 'html', 'xlsx', 'pdf', 'all']</td><td style="word-wrap: break-word;">Select output format: text (plain text + JSON), html (HTML table), xlsx (Excel), pdf (PDF), or all (returns all available formats).</td><td style="word-wrap: break-word;">all</td></tr>
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
<tr><td style="word-wrap: break-word;">text_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary or table text of the query results. Included for text and all formats.</td><td style="word-wrap: break-word;">Oracle Visual Query: employees Rows: 10 Columns: employee_id, first_name, last_name, department_name</td></tr>
<tr><td style="word-wrap: break-word;">json_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw query results in JSON string form. Always included for text and all formats; included alongside other formats when selected individually.</td><td style="word-wrap: break-word;">[{"EMPLOYEE_ID":101,"FIRST_NAME":"Neena","LAST_NAME":"Kochhar","DEPARTMENT_NAME":"Executive"}]</td></tr>
<tr><td style="word-wrap: break-word;">html_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML table rendering of the results when output format is html or all.</td><td style="word-wrap: break-word;"><table><thead><tr><th>EMPLOYEE_ID</th><th>FIRST_NAME</th>...</tr></thead><tbody>...</tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_file</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Excel workbook (XLSX) containing the results when output format is xlsx or all.</td><td style="word-wrap: break-word;"><bytes of .xlsx file></td></tr>
<tr><td style="word-wrap: break-word;">pdf_file</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">PDF document containing tabular results when output format is pdf or all.</td><td style="word-wrap: break-word;"><bytes of .pdf file></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **JOIN configuration must be valid JSON**: If join_config is invalid JSON, JOINs are skipped.
- **Schema prefixing**: When 'schema' is provided, the main table is referenced as schema.main_table.
- **Row limiting**: The query uses 'FETCH FIRST n ROWS ONLY' when limit_rows > 0.
- **SQL validity**: where_conditions, group_by_columns, having_conditions, and order_by_columns must be valid Oracle SQL fragments (do not include the keywords themselves).
- **Selected columns**: Use '*' to select all columns or a comma-separated list for specific columns.
- **Outputs by format**: 'text' returns text + JSON; 'html' returns JSON + HTML; 'xlsx' returns JSON + XLSX; 'pdf' returns JSON + PDF; 'all' returns text, JSON, HTML, XLSX, and PDF.

## Troubleshooting
- **Invalid join_config JSON**: Ensure join_config is a well-formed JSON array. Example: [{"type":"INNER","table":"departments","on":"employees.department_id = departments.department_id"}].
- **SQL errors from database**: Verify column names, table names, and syntax in where_conditions/group_by_columns/having_conditions/order_by_columns.
- **No rows returned**: Remove or adjust filters in where_conditions and verify the main_table/schema. Temporarily set limit_rows to 0 to see all results.
- **Row limit not applied**: Some Oracle environments may not support FETCH FIRST syntax; consider adding a ROWNUM filter in where_conditions (e.g., ROWNUM <= 100) as a workaround.
- **Permission errors**: Confirm credentials have access to the specified schema and tables.
- **Large result sets causing timeouts**: Increase timeout, apply stricter filters, or reduce limit_rows.
