# Oracle Visual Query Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds and runs Oracle SQL queries using visual-style inputs (tables, joins, filters) without writing SQL. Supports JOINs, WHERE, GROUP BY, HAVING, ORDER BY, and row limits. Results can be returned in text/JSON by default, or exported in HTML/XLSX/PDF when requested.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclevisualquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to query Oracle data by specifying tables, join relationships, and conditions via parameters. It fits into workflows that retrieve tabular data for reporting or analysis, optionally exporting results to HTML, Excel, or PDF. Provide Oracle credentials, set the main table and optional schema, define selected columns and joins, add filters/grouping/sorting, and choose the output format.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored Oracle credentials configured for the service.</td><td style="word-wrap: break-word;">/connections/oracle/default.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Request timeout in seconds for executing the query.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">main_table</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary table to query from.</td><td style="word-wrap: break-word;">employees</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional schema name to qualify tables. Leave empty to use the current user schema.</td><td style="word-wrap: break-word;">HR</td></tr>
<tr><td style="word-wrap: break-word;">selected_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of columns to select, or * for all columns.</td><td style="word-wrap: break-word;">employee_id, first_name, last_name</td></tr>
<tr><td style="word-wrap: break-word;">join_config</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array describing JOINs to include. Each item can include type (INNER/LEFT/RIGHT), table, optional schema, and the ON clause.</td><td style="word-wrap: break-word;">[{"type":"INNER","table":"departments","schema":"HR","on":"employees.department_id = departments.department_id"}]</td></tr>
<tr><td style="word-wrap: break-word;">where_conditions</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw WHERE conditions without the WHERE keyword.</td><td style="word-wrap: break-word;">salary > 50000 AND department_id = 10</td></tr>
<tr><td style="word-wrap: break-word;">group_by_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of columns for GROUP BY.</td><td style="word-wrap: break-word;">department_id</td></tr>
<tr><td style="word-wrap: break-word;">having_conditions</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw HAVING conditions without the HAVING keyword. Only applies if GROUP BY is used.</td><td style="word-wrap: break-word;">COUNT(*) > 5</td></tr>
<tr><td style="word-wrap: break-word;">order_by_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of ORDER BY expressions. Append DESC for descending order.</td><td style="word-wrap: break-word;">salary DESC, last_name</td></tr>
<tr><td style="word-wrap: break-word;">limit_rows</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Limit the number of rows returned (0 means no limit).</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">['text','html','xlsx','pdf','all']</td><td style="word-wrap: break-word;">Choose the output format. 'text' returns plain text plus JSON. 'html', 'xlsx', or 'pdf' produce those formats. 'all' returns all available formats.</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the query results, including a title and possibly a preview.</td><td style="word-wrap: break-word;">Oracle Visual Query: employees — 10 rows returned</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded full result payload, including rows and metadata as returned by the service.</td><td style="word-wrap: break-word;">{"rows":[{"EMPLOYEE_ID":100,"FIRST_NAME":"Steven"}],"count":1}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML table representation of the results, populated when the node generates HTML or when 'all' is selected.</td><td style="word-wrap: break-word;"><table><thead><tr><th>EMPLOYEE_ID</th>...</tr></thead><tbody>...</tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A reference or encoded content for an Excel (XLSX) export of the results, populated when XLSX or 'all' is selected.</td><td style="word-wrap: break-word;"><xlsx-file-reference-or-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A reference or encoded content for a PDF export of the results, populated when PDF or 'all' is selected.</td><td style="word-wrap: break-word;"><pdf-file-reference-or-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Join configuration must be valid JSON**: If join_config is not valid JSON, joins are skipped and a warning is logged.
- **Schema is optional**: If provided, it prefixes the main table and joined tables to fully qualify them.
- **Limit behavior**: Row limiting uses 'FETCH FIRST n ROWS ONLY'. Ensure your Oracle version supports it (Oracle 12c+).
- **Output selection**: Use output_format='all' to generate exports (HTML/XLSX/PDF). Otherwise, the default returns text and JSON.
- **Raw clauses**: where_conditions, having_conditions, and order_by_columns are inserted as-is; ensure proper Oracle SQL syntax to avoid errors.

## Troubleshooting
- **Invalid join_config JSON**: Replace join_config with a valid JSON array. Example format: [{"type":"INNER","table":"departments","schema":"HR","on":"employees.department_id = departments.department_id"}].
- **No results or empty output**: Check where_conditions and joins for over-restriction; test with fewer filters or limit_rows=0.
- **Oracle version errors for FETCH FIRST**: If you see syntax errors, your Oracle version may not support FETCH FIRST. Remove limit_rows or use equivalent ROWNUM filters in where_conditions.
- **Permission or schema errors (ORA-xxxx)**: Verify credentials and schema/table names. Try leaving schema empty to use the current user schema.
- **Timeouts**: Increase the timeout input or optimize the query (fewer joins, narrower columns, add filters).
