# Oracle Query Builder Helper

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Assists with interactive Oracle query building tasks: discover tables in a schema, list columns for a table, suggest basic joins, and generate and run a simple query from natural language requirements. Returns results in text/JSON by default, with optional export formats.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclequerybuilder.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node early in a workflow to explore an Oracle schema and quickly assemble a starter query. Typical steps: 1) discover tables in a schema, 2) inspect columns of a target table, 3) request suggested joins, 4) describe desired data to build and run a simple query. Choose your preferred output format for display or export.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Path or reference to stored Oracle credentials used by the service integration.</td><td style="word-wrap: break-word;"><path-to-oracle-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation to complete.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Operation to perform: discover_tables, get_table_columns, suggest_joins, or build_query.</td><td style="word-wrap: break-word;">discover_tables</td></tr>
<tr><td style="word-wrap: break-word;">target_schema</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema to explore. Leave empty to use the current user schema.</td><td style="word-wrap: break-word;">HR</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Table to inspect or base for join suggestions. Required for get_table_columns and suggest_joins.</td><td style="word-wrap: break-word;">employees</td></tr>
<tr><td style="word-wrap: break-word;">query_requirements</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Natural language description of the data you want. Used when action is build_query.</td><td style="word-wrap: break-word;">List employees and their department names</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Select output format: text, html, xlsx, pdf, or all (generates all formats if available).</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary and, when applicable, query results in plain text.</td><td style="word-wrap: break-word;">Oracle Tables in Schema: HR</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON payload of the operation result (tables, columns, suggestions, or query results).</td><td style="word-wrap: break-word;">{"tables":[{"table_name":"EMPLOYEES"}]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML table rendering of results when output_format is html or all.</td><td style="word-wrap: break-word;"><table><tr><th>EMPLOYEE_ID</th>...</tr>...</table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary Excel content for results when output_format is xlsx or all.</td><td style="word-wrap: break-word;"><xlsx-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF content for results when output_format is pdf or all.</td><td style="word-wrap: break-word;"><pdf-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- The node uses stored Oracle credentials; ensure credentials_path is valid and authorized.
- get_table_columns and suggest_joins require table_name.
- When action is build_query, the generated query is a simplified heuristic based on the text in query_requirements; review before using in production.
- If target_schema is empty, operations default to the current user schema.
- Choosing output_format = all attempts to produce text/JSON plus HTML, XLSX, and PDF variants if supported.

## Troubleshooting
- Error: Unknown action: Verify the action is one of discover_tables, get_table_columns, suggest_joins, build_query.
- No results when discovering tables: Confirm target_schema exists and the credentials have privileges to list objects.
- Column discovery fails: Provide a valid table_name and ensure the table is accessible in the specified or current schema.
- Join suggestions seem generic: Suggestions are template-based; refine by specifying a precise table_name and schema.
- Timeouts: Increase the timeout value for large schemas or heavy queries, or optimize the schema filters.
