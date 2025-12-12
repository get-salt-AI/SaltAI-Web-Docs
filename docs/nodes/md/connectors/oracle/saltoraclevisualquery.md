# Oracle Visual Query Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds and executes Oracle SQL queries using a visual, form-driven interface. Supports selecting columns, JOINs across tables, WHERE/GROUP BY/HAVING/ORDER BY clauses, and row limits. Results can be returned in text/JSON by default, or exported to HTML/XLSX/PDF when requested.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclevisualquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to query Oracle data without hand-writing SQL. Provide a main table, optional schema, selected columns, and an optional JSON join configuration. You can also include filter conditions, grouping, ordering, and a row limit. Choose the desired output format to receive text/JSON or downloadable exports. Typical workflow: discover tables/columns with Oracle List Tables/Table Info, then configure and run this Visual Query to retrieve and export results.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored Oracle credentials configured for this node.</td><td style="word-wrap: break-word;">/configs/credentials/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Request timeout in seconds for the database operation.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">main_table</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Main table to query from.</td><td style="word-wrap: break-word;">employees</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema name to qualify tables. Leave empty to use the current user schema.</td><td style="word-wrap: break-word;">HR</td></tr>
<tr><td style="word-wrap: break-word;">selected_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Columns to select, comma-separated. Use '*' to select all columns.</td><td style="word-wrap: break-word;">employee_id, first_name, last_name</td></tr>
<tr><td style="word-wrap: break-word;">join_config</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array defining JOINs. Each item: {"type": "INNER\|LEFT\|RIGHT\|FULL", "table": "...", "schema": "...", "on": "join_condition"}.</td><td style="word-wrap: break-word;">[{"type":"INNER","table":"departments","schema":"HR","on":"employees.department_id = departments.department_id"}]</td></tr>
<tr><td style="word-wrap: break-word;">where_conditions</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">WHERE clause conditions without the 'WHERE' keyword.</td><td style="word-wrap: break-word;">salary > 50000 AND department_id = 10</td></tr>
<tr><td style="word-wrap: break-word;">group_by_columns</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">GROUP BY columns, comma-separated.</td><td style="word-wrap: break-word;">department_id</td></tr>
<tr><td style="word-wrap: break-word;">having_conditions</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HAVING clause conditions without the 'HAVING' keyword.</td><td style="word-wrap: break-word;">COUNT(*) > 5</td></tr>
<tr><td style="word-wrap: break-word;">order_by_columns</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">ORDER BY columns, comma-separated. Append DESC for descending.</td><td style="word-wrap: break-word;">salary DESC, employee_id</td></tr>
<tr><td style="word-wrap: break-word;">limit_rows</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of rows to return (0 = no limit). Uses FETCH FIRST <n> ROWS ONLY.</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">CHOICE[text, html, xlsx, pdf, all]</td><td style="word-wrap: break-word;">Select output format. 'text' returns readable text plus JSON; 'all' returns text/JSON and export formats.</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary or tabular text of the query results.</td><td style="word-wrap: break-word;">Oracle Visual Query: employees (10 rows)...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON payload including rows/metadata from the executed query.</td><td style="word-wrap: break-word;">{"rows":[{"EMPLOYEE_ID":100,"FIRST_NAME":"Steven"}],"row_count":1}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML table representation of the results when requested.</td><td style="word-wrap: break-word;"><table><thead><tr><th>EMPLOYEE_ID</th>...</tr></thead><tbody>...</tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">FILE</td><td style="word-wrap: break-word;">Excel file containing the query results when export is requested.</td><td style="word-wrap: break-word;"><binary-xlsx-file-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">FILE</td><td style="word-wrap: break-word;">PDF file containing a formatted table of results when export is requested.</td><td style="word-wrap: break-word;"><binary-pdf-file-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- The JOIN configuration must be valid JSON. If it is invalid, JOINs are skipped.
- Schema prefixes are applied as schema.table when a schema is provided; otherwise the current user schema is used.
- Row limiting uses FETCH FIRST <n> ROWS ONLY, which requires Oracle 12c or later. For older versions, consider handling limits in your WHERE clause.
- Ensure column names, table names, and conditions match your database schema to avoid execution errors.
- Only the selected output format is populated unless 'all' is chosen, in which case text/JSON/HTML/XLSX/PDF are all produced.

## Troubleshooting
- Invalid join_config JSON: Fix the JSON format (e.g., ensure proper quotes and brackets). The node logs a warning and runs without JOINs.
- ORA- errors or missing table/column: Verify schema and object names in main_table, selected_columns, and join_config.
- Permission or connectivity issues: Confirm credentials_path is correct and the user has required privileges; increase timeout for large queries.
- No rows returned: Check where_conditions, group_by/having constraints, or increase limit_rows.
- Export files empty: Make sure output_format is set to 'html', 'xlsx', 'pdf', or 'all'. If 'text' is selected, only text/JSON outputs are populated.
