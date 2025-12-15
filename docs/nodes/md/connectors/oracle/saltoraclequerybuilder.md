# Oracle Query Builder Helper

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Interactive helper for exploring an Oracle database and assembling queries without deep SQL knowledge. It can discover tables, list a table's columns, suggest potential JOINs, and build/execute a basic query from natural language requirements. Outputs can be returned as plain text with JSON, or exported to HTML, XLSX, and PDF formats.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclequerybuilder.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to understand your Oracle schema and quickly assemble queries. Typical workflow: start with discover_tables to see available tables in a schema, use get_table_columns for structure, run suggest_joins to see likely relationships, then use build_query to generate and execute a basic query from a description. Choose output_format to get helper results or executed query results in text/HTML/XLSX/PDF.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the stored Oracle credentials used by the node to authenticate.</td><td style="word-wrap: break-word;"><path-to-oracle-credentials.json></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the service to respond before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Operation to perform. Options: discover_tables, get_table_columns, suggest_joins, build_query.</td><td style="word-wrap: break-word;">discover_tables</td></tr>
<tr><td style="word-wrap: break-word;">target_schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Oracle schema name to explore. Leave empty to use the current user schema.</td><td style="word-wrap: break-word;">HR</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Table name to inspect (used by get_table_columns and suggest_joins). Can be left empty for other actions.</td><td style="word-wrap: break-word;">EMPLOYEES</td></tr>
<tr><td style="word-wrap: break-word;">query_requirements</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Natural language description of the data you want (used by build_query).</td><td style="word-wrap: break-word;">Find employees and their department names</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Desired output format. Options: text (plain text + JSON), html (HTML table), xlsx (Excel), pdf (PDF), all (returns exports as available).</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable textual result, such as table/column listings, join suggestions, or query results.</td><td style="word-wrap: break-word;">Oracle Tables in Schema: HR EMPLOYEES DEPARTMENTS LOCATIONS</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-serialized data payload of the operation (e.g., lists of tables/columns, suggestions, or query results).</td><td style="word-wrap: break-word;">{"suggestions": {"table": "EMPLOYEES", "suggested_joins": [{"type": "INNER", "table": "DEPARTMENTS", "schema": "HR", "on": "EMPLOYEES.DEPARTMENT_ID = DEPARTMENTS.DEPARTMENT_ID"}]}}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML rendition for display (populated when output_format is html or all).</td><td style="word-wrap: break-word;"><h3>Oracle Join Suggestions: EMPLOYEES</h3><pre>...</pre></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Binary XLSX export containing the results (populated when output_format is xlsx or all).</td><td style="word-wrap: break-word;"><base64-xlsx-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">Binary PDF export containing the results (populated when output_format is pdf or all).</td><td style="word-wrap: break-word;"><base64-pdf-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Action-specific requirements**: table_name is required for get_table_columns and suggest_joins. query_requirements is used by build_query.
- **Schema handling**: target_schema can be left empty to use the current user schema.
- **Execution behavior**: build_query generates a basic heuristic query and executes it; the executed SQL is returned in the JSON under built_query.
- **Output formats**: text returns text and JSON. html/xlsx/pdf return only that export plus JSON. all includes exports via the service and may include text where applicable.
- **Credentials**: Ensure the credentials_path points to valid Oracle credentials with appropriate permissions to list tables and run queries.

## Troubleshooting
- **Missing table name**: If you select get_table_columns or suggest_joins without a table_name, set table_name and rerun.
- **Unknown action**: If you see an "Unknown action" error, ensure action is one of discover_tables, get_table_columns, suggest_joins, or build_query.
- **No results returned**: Verify target_schema and table_name exist and your credentials have access. Try discover_tables first to confirm visibility.
- **Connection or auth errors**: Confirm credentials_path is correct and valid for the Oracle instance. Test with the Oracle Test Connection node or verify network access.
- **Timeouts**: Increase timeout for large schemas or complex queries, or reduce result size by narrowing the request.
