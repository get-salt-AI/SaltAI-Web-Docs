# Oracle Visual Query Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds and executes Oracle SQL SELECT queries using a visual, parameter-driven interface. Supports JOINs via JSON configuration, WHERE/GROUP BY/HAVING/ORDER BY clauses, and optional row limits. Results can be returned in multiple formats, including text, HTML, Excel, and PDF.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclevisualquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to assemble complex Oracle queries without manually writing SQL. It is ideal for combining tables with JOINs, filtering with conditions, grouping, and ordering. Connect it to your Oracle credentials and set visual parameters to generate and run the query, then choose the desired output format for downstream use.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">CREDENTIALS</td><td style="word-wrap: break-word;">Path or reference to Oracle credentials configured for the service.</td><td style="word-wrap: break-word;"><oracle-credentials-ref></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before timing out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">main_table</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary table to select from.</td><td style="word-wrap: break-word;">employees</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema name to qualify tables. Leave empty to use the current user schema.</td><td style="word-wrap: break-word;">HR</td></tr>
<tr><td style="word-wrap: break-word;">selected_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Columns to return. Use comma-separated list or * to select all.</td><td style="word-wrap: break-word;">employee_id, first_name, last_name</td></tr>
<tr><td style="word-wrap: break-word;">join_config</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array describing JOINs. Each item should include type, table, optional schema, and on condition.</td><td style="word-wrap: break-word;">[{"type": "INNER", "table": "departments", "schema": "HR", "on": "employees.department_id = departments.department_id"}]</td></tr>
<tr><td style="word-wrap: break-word;">where_conditions</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw WHERE clause conditions without the WHERE keyword.</td><td style="word-wrap: break-word;">salary > 50000 AND department_id = 10</td></tr>
<tr><td style="word-wrap: break-word;">group_by_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated columns for GROUP BY.</td><td style="word-wrap: break-word;">department_id</td></tr>
<tr><td style="word-wrap: break-word;">having_conditions</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw HAVING clause conditions without the HAVING keyword.</td><td style="word-wrap: break-word;">COUNT(*) > 5</td></tr>
<tr><td style="word-wrap: break-word;">order_by_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated ORDER BY expressions. Add DESC for descending.</td><td style="word-wrap: break-word;">salary DESC, last_name</td></tr>
<tr><td style="word-wrap: break-word;">limit_rows</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of rows to return (0 means no limit).</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Format of the output: text (plain text + JSON), html (HTML table), xlsx (Excel), pdf (PDF), or all (all formats).</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary or table preview of the query results.</td><td style="word-wrap: break-word;">Oracle Visual Query: employees — 100 rows returned</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw result data serialized as JSON.</td><td style="word-wrap: break-word;">{"rows": [{"EMPLOYEE_ID": 101, "FIRST_NAME": "Neena"}], "row_count": 10}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML table representation of the results (available when output_format is html or all).</td><td style="word-wrap: break-word;"><table><tr><th>EMPLOYEE_ID</th>...</tr>...</table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Excel file bytes/handle for the results (available when output_format is xlsx or all).</td><td style="word-wrap: break-word;"><binary-xlsx-data></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF file bytes/handle for the results (available when output_format is pdf or all).</td><td style="word-wrap: break-word;"><binary-pdf-data></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Selected columns accept '*' or a comma-separated list. Ensure column names are correctly qualified if ambiguous.
- join_config must be valid JSON. Invalid JSON is ignored and JOINs are skipped with a warning.
- Schema is optional; when omitted, the current user schema is used. JOIN targets can also specify their own schema.
- WHERE, GROUP BY, HAVING, and ORDER BY inputs are raw SQL snippets; no validation is performed.
- Row limiting uses 'FETCH FIRST n ROWS ONLY' which requires Oracle 12c or later. For older versions, results may not limit as expected.
- Choosing output_format = 'all' returns all supported formats; otherwise, a default text/JSON response is provided.

## Troubleshooting
- Query returns no results: Verify where_conditions and join_config ON clauses. Test the base table and relax filters.
- Invalid JSON in join_config: Provide well-formed JSON array with objects that include type, table, and on. Example format is shown in the input tooltip.
- ORA-00942: table or view does not exist: Confirm schema and table names. Provide the schema parameter or qualify tables in join_config.
- Permission errors: Ensure the credentials have SELECT privileges on all referenced tables and schemas.
- Output not limited: If using an Oracle version prior to 12c, FETCH FIRST may not work. Set limit_rows to 0 and add a ROWNUM filter in where_conditions.
- Timeouts: Increase the timeout input or simplify the query (reduce JOINs, filters, or result size).
