# Oracle Query Builder Helper

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

An interactive Oracle query builder that helps you explore schemas and tables, inspect columns, get basic join suggestions, and auto-build simple queries from plain-language requirements. It can also execute the built query and return results in multiple formats.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclequerybuilder.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need guided discovery and quick query prototyping against an Oracle database. Typical workflow: first discover tables in a schema, inspect a table’s columns, get suggested joins, and then build and run a basic query from your requirements. Choose an output format to control whether you want text/JSON only or also rich exports like HTML, XLSX, and PDF.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">CREDENTIALS</td><td style="word-wrap: break-word;">Path or reference to the saved Oracle credentials to authenticate requests.</td><td style="word-wrap: break-word;"><path-to-oracle-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation to complete.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">STRING (one of: discover_tables, get_table_columns, suggest_joins, build_query)</td><td style="word-wrap: break-word;">What operation to perform: discover tables in a schema, get columns for a table, suggest possible joins, or build a simple query from requirements.</td><td style="word-wrap: break-word;">discover_tables</td></tr>
<tr><td style="word-wrap: break-word;">target_schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema to explore. Leave empty to use the current user schema.</td><td style="word-wrap: break-word;">HR</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target table for column discovery or join suggestions. Required for get_table_columns and suggest_joins.</td><td style="word-wrap: break-word;">employees</td></tr>
<tr><td style="word-wrap: break-word;">query_requirements</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Plain-language description of the data you want. Used when action is build_query.</td><td style="word-wrap: break-word;">List employees with their department names and only include those hired after 2020</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">STRING (one of: text, html, xlsx, pdf, all)</td><td style="word-wrap: break-word;">Controls the output format. 'text' returns plain text and JSON; 'all' also includes HTML, XLSX, and PDF exports.</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary or tabular text of the results.</td><td style="word-wrap: break-word;">Oracle Join Suggestions: employees ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Machine-readable JSON string of the results, including any suggestions or built query.</td><td style="word-wrap: break-word;">{"suggestions": {"table": "employees", "suggested_joins": [...]}}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML table representation of the results (populated when output_format is 'all').</td><td style="word-wrap: break-word;"><table><tr><th>employee_id</th>...</tr>...</table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary XLSX content of the results (populated when output_format is 'all').</td><td style="word-wrap: break-word;"><binary-xlsx-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF content of the results (populated when output_format is 'all').</td><td style="word-wrap: break-word;"><binary-pdf-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- The 'get_table_columns' and 'suggest_joins' actions require 'table_name'.
- Leaving 'target_schema' empty means the current user schema is used.
- Join suggestions and query generation are simplified and heuristic-based; review and refine the output SQL as needed.
- If you select 'all' for output_format, the node attempts to provide text/JSON and export formats (HTML, XLSX, PDF). For other formats, only text/JSON are returned.
- Queries execute against the configured Oracle connection; ensure credentials and access are correct.

## Troubleshooting
- If you receive an error stating table_name is required, set 'table_name' when using 'get_table_columns' or 'suggest_joins'.
- If no tables are returned for discover_tables, verify 'target_schema' and your user permissions.
- If the built query is too generic or not aligned with your needs, clarify 'query_requirements' with specific tables, columns, and filters.
- If the node times out, increase 'timeout' or simplify the requested operation.
- If exports (HTML/XLSX/PDF) are empty, set 'output_format' to 'all' to include them; otherwise only text/JSON are returned.
