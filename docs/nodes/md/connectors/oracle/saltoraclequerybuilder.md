# Oracle Query Builder Helper

An interactive helper for exploring Oracle schemas and drafting queries. It can discover tables, list columns, suggest simple JOIN relationships, and generate a basic query from natural-language requirements. Results can be returned as text/JSON or exported to HTML, Excel, and PDF based on the chosen output format.

## Usage

Use this node when you need guided exploration of an Oracle database and a quick way to assemble starter queries. Typical workflow: provide credentials, use discover_tables to enumerate tables in a schema, get_table_columns to inspect a table, suggest_joins for basic relationship hints, and build_query to produce a simple query and run it. Choose output_format to control whether you get rich exports or just text/JSON.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to saved Oracle credentials (matches the 'oracle' credential template).</td><td style="word-wrap: break-word;">/data/credentials/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Request timeout in seconds for operations executed by this node.</td><td style="word-wrap: break-word;">120</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Operation to perform. Options: discover_tables (list tables), get_table_columns (list columns for a table), suggest_joins (basic join hints), build_query (generate and execute a simple query from requirements).</td><td style="word-wrap: break-word;">discover_tables</td></tr>
<tr><td style="word-wrap: break-word;">target_schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema to explore. Leave empty to use the current user schema.</td><td style="word-wrap: break-word;">HR</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Table name to inspect or use for join suggestions. Required for get_table_columns and suggest_joins; otherwise unused.</td><td style="word-wrap: break-word;">EMPLOYEES</td></tr>
<tr><td style="word-wrap: break-word;">query_requirements</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Natural-language description of what you want to retrieve for the build_query action.</td><td style="word-wrap: break-word;">List employees with their department names where salary is above 70000</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Controls the format of the returned results. Options: text, html, xlsx, pdf, all. The 'all' option returns all formats.</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable text summary or table preview of the result. Always included when output_format is text or all.</td><td style="word-wrap: break-word;">Oracle Tables in Schema: HR - EMPLOYEES - DEPARTMENTS ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-serialized data for programmatic use, including any result metadata. Always included when output_format is text or all.</td><td style="word-wrap: break-word;">{"tables": ["EMPLOYEES", "DEPARTMENTS"]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML table markup for rendering results. Included when output_format is html or all; otherwise empty.</td><td style="word-wrap: break-word;"><table><thead><tr><th>TABLE_NAME</th></tr></thead><tbody><tr><td>EMPLOYEES</td></tr></tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A reference or encoded payload for an Excel export of the result set. Included when output_format is xlsx or all; otherwise empty.</td><td style="word-wrap: break-word;"><xlsx-binary-or-reference></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A reference or encoded payload for a PDF export of the result set. Included when output_format is pdf or all; otherwise empty.</td><td style="word-wrap: break-word;"><pdf-binary-or-reference></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Action-specific inputs**: table_name is required for get_table_columns and suggest_joins; query_requirements is used by build_query.
- **Output selection**: output_format controls which outputs are populated. Use 'all' to receive text, json, html, xlsx, and pdf together.
- **Discover vs. execute**: discover_tables, get_table_columns, and suggest_joins return metadata. build_query constructs a simple query and executes it.
- **Join suggestions are basic**: The join suggestions are heuristic and meant as starting points; validate them against your schema.
- **Schema handling**: Leaving target_schema empty uses the current user schema on the Oracle instance.
- **Credentials**: Ensure the credentials at credentials_path match the 'oracle' template and have permissions to list schemas/tables and run queries.

## Troubleshooting
- **Missing table_name**: For get_table_columns or suggest_joins, provide table_name. The node will raise an error if it's empty.
- **No tables returned**: Verify target_schema is correct and the user has access. Try leaving target_schema empty to use the default schema.
- **Timeouts**: Increase the timeout value for large schemas or long-running queries.
- **Permission errors**: Ensure the provided credentials have SELECT privileges on the target schema/tables.
- **Empty exports**: If html/xlsx/pdf outputs are empty, confirm output_format includes those formats (e.g., set to html/xlsx/pdf or all).
- **Unexpected query from build_query**: The builder is simplistic; refine query_requirements or manually edit the resulting SQL using a separate query node.
