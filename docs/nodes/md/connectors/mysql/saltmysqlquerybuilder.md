# MySQL Query Builder Helper

Assists in exploring a MySQL database and composing queries without writing SQL directly. It can list tables, inspect table columns, suggest JOINs based on foreign keys, and generate high-level query suggestions from a natural language description. Results can be returned as text, JSON, or exported to HTML/XLSX/PDF depending on the selected output format.

## Usage

Use this node when you need to discover schema details or get guidance to build queries. Typical workflow: start with discover_tables to see available tables, then get_table_columns for a specific table, use suggest_joins to understand relationships, and optionally build_query to get example query patterns from a plain-language requirement. Feed the insights into a visual query builder or a direct query execution node.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Path or reference to stored MySQL credentials used to authenticate requests.</td><td style="word-wrap: break-word;"><path-to-mysql-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the database operation before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select what the helper should do: discover tables, get columns for a table, suggest joins, or provide query-building suggestions.</td><td style="word-wrap: break-word;">discover_tables</td></tr>
<tr><td style="word-wrap: break-word;">target_database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database/schema to explore.</td><td style="word-wrap: break-word;">sales_db</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Table name to inspect columns or generate join suggestions for. Required when action is get_table_columns or suggest_joins.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">query_requirements</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Natural language description of the data you want (used when action is build_query to produce example patterns).</td><td style="word-wrap: break-word;">List users with their most recent order total for the last 30 days</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Format of the output. text returns a readable summary with JSON; html returns an HTML segment; xlsx returns an Excel file; pdf returns a PDF; all returns text and JSON (helper results do not generate all export types).</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the result (tables list, columns detail, join suggestions, or sample query patterns). May be empty depending on the selected output_format.</td><td style="word-wrap: break-word;">Tables in Database: sales_db - users - orders - products</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-serialized result payload containing structured data (e.g., suggestions, table info).</td><td style="word-wrap: break-word;">{"suggestions": ["JOIN orders ON users.id = orders.user_id"]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of the result when output_format is html.</td><td style="word-wrap: break-word;"><h3>Join Suggestions: sales_db.users</h3><pre>JOIN orders ON users.id = orders.user_id</pre></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary Excel data when output_format is xlsx (simple tabular rendering of the helper result).</td><td style="word-wrap: break-word;"><bytes-of-xlsx></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF data when output_format is pdf (simple rendering of the helper result).</td><td style="word-wrap: break-word;"><bytes-of-pdf></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Action-specific requirements**: table_name is required for get_table_columns and suggest_joins; it is not used for discover_tables.
- **Credentials required**: A valid MySQL credential reference must be supplied via credentials_path; the node will not execute without it.
- **Join suggestions logic**: suggest_joins inspects information_schema.KEY_COLUMN_USAGE for foreign key relationships in the target_database; if no foreign keys exist, suggestions will indicate none found.
- **Output format behavior**: For helper actions, the node primarily returns text and JSON. When html/xlsx/pdf is requested, a simple representation of the helper output is generated.
- **Natural language builder**: build_query returns example SQL patterns and guidance based on the query_requirements; it does not generate an executable query for your exact schema.
- **Timeouts and permissions**: Large schemas or restricted permissions can affect results; ensure the user has access to information_schema and target tables.

## Troubleshooting
- **Missing table_name error**: If you select get_table_columns or suggest_joins without a table_name, provide a valid table and retry.
- **Empty suggestions**: If suggest_joins returns no relationships, verify that foreign keys exist in the schema and that the database user can read information_schema.
- **Authentication or connection failures**: Confirm credentials_path points to valid MySQL credentials and the database is reachable; increase timeout if necessary.
- **Unexpected output format**: If html/xlsx/pdf outputs are empty, ensure output_format is set appropriately; text will always include a readable summary when available.
- **Incorrect database target**: If discover_tables returns an empty set, confirm target_database is correct and the user has privileges to list tables.
