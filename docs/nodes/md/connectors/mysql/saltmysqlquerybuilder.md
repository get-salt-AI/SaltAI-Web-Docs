# MySQL Query Builder Helper

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

An interactive helper for exploring a MySQL database and scaffolding queries. It can list tables, inspect columns, suggest JOINs based on foreign keys, and produce simple query-building guidance from a natural language description. Results can be returned as plain text with JSON, or formatted as HTML/XLSX/PDF depending on the output choice.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlquerybuilder.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to understand a MySQL schema and quickly assemble queries without writing SQL from scratch. Typical workflow: authenticate with your MySQL credentials, choose an action (discover tables, get columns, suggest joins, or build query), optionally specify a target table, and select the desired output format for downstream nodes or export.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">CREDENTIALS</td><td style="word-wrap: break-word;">Path or reference to stored MySQL credentials used to authenticate requests.</td><td style="word-wrap: break-word;"><path-to-mysql-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the database helper actions before timing out.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">What to do: 'discover_tables' lists tables in the target database; 'get_table_columns' returns column metadata for a given table; 'suggest_joins' suggests JOIN clauses based on foreign keys; 'build_query' generates simple guidance from a natural language description.</td><td style="word-wrap: break-word;">discover_tables</td></tr>
<tr><td style="word-wrap: break-word;">target_database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The database/schema to explore.</td><td style="word-wrap: break-word;">sales_db</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The table to inspect or use for join suggestions. Required for 'get_table_columns' and 'suggest_joins'.</td><td style="word-wrap: break-word;">orders</td></tr>
<tr><td style="word-wrap: break-word;">query_requirements</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A natural language description of what data you want to retrieve. Used when action='build_query' to return guiding patterns.</td><td style="word-wrap: break-word;">List users with their last 3 orders and total spend this year</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Format of the output. 'text' returns plain text plus JSON; 'html' returns an HTML block with JSON; 'xlsx' produces an Excel file with JSON; 'pdf' produces a PDF with JSON; 'all' behaves like text for this helper.</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable result text. Populated when output_format is 'text' or 'all'.</td><td style="word-wrap: break-word;">Tables in Database: sales_db orders order_items customers ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Machine-readable JSON containing the same information as the text output (e.g., lists of tables, columns, or join suggestions).</td><td style="word-wrap: break-word;">{"tables":["orders","order_items","customers"]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML-formatted content for rendering in UI. Populated when output_format = 'html'.</td><td style="word-wrap: break-word;"><h3>Tables in Database: sales_db</h3><pre>orders\norder_items\ncustomers</pre></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Excel workbook bytes for export. Populated when output_format = 'xlsx'.</td><td style="word-wrap: break-word;"><bytes-of-xlsx-file> </td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF document bytes for export. Populated when output_format = 'pdf'.</td><td style="word-wrap: break-word;"><bytes-of-pdf-file></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Action requirements: 'get_table_columns' and 'suggest_joins' require 'table_name'; 'discover_tables' and 'build_query' do not.
- Join suggestions are based on information_schema foreign key metadata; if no foreign keys exist or permissions are limited, suggestions may be empty.
- The 'build_query' action provides generic patterns and guidance, not a fully generated SQL query.
- Output population depends on 'output_format': only the corresponding output fields are filled; others are empty strings.
- Ensure credentials are valid and the user has sufficient privileges to access information_schema and target databases/tables.

## Troubleshooting
- No results for 'discover_tables': Verify 'target_database' exists and the credentials have permissions to list tables.
- Empty or missing JOIN suggestions: Confirm foreign keys are defined in the schema and accessible via information_schema with current permissions.
- Error: 'Table name is required' for certain actions: Provide 'table_name' when using 'get_table_columns' or 'suggest_joins'.
- Timeouts or connectivity errors: Increase 'timeout', verify host/network access, and confirm credentials are correct.
- Unexpected output format behavior: Check 'output_format' value; only the matching output field is populated for html/xlsx/pdf modes.
