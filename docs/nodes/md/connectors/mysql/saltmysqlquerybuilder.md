# MySQL Query Builder Helper

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

An interactive helper for exploring a MySQL database and guiding query construction. It can list tables, show a table’s columns, suggest JOINs based on foreign keys, and provide template SQL patterns from a natural-language description. Results can be returned as plain text with JSON, or optionally formatted as HTML, XLSX, or PDF.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlquerybuilder.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to understand a MySQL database’s structure and get guidance building queries without writing SQL from scratch. Typical workflow: first discover tables in a database, then inspect a specific table’s columns, optionally request JOIN suggestions for that table, and finally ask for query-building suggestions from a natural-language requirement. Choose the output_format to match how you plan to consume or export the results.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored MySQL service credentials used for authentication.</td><td style="word-wrap: break-word;">/configs/credentials/mysql.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for backend operations before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The helper action to perform.</td><td style="word-wrap: break-word;">discover_tables</td></tr>
<tr><td style="word-wrap: break-word;">target_database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The database (schema) to explore.</td><td style="word-wrap: break-word;">analytics_db</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target table for column discovery or join suggestions. Required when action is get_table_columns or suggest_joins.</td><td style="word-wrap: break-word;">orders</td></tr>
<tr><td style="word-wrap: break-word;">query_requirements</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Natural-language description of the data you want to retrieve. Used when action is build_query to provide example SQL patterns and guidance.</td><td style="word-wrap: break-word;">List each user with their total number of completed orders this year.</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select how results are returned: text (text + JSON), html, xlsx, pdf, or all.</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the result (tables, columns, join suggestions, or query guidance). Present when output_format is text or all.</td><td style="word-wrap: break-word;">Tables in Database: analytics_db - users - orders - products</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured JSON containing the same information as the text output for downstream use.</td><td style="word-wrap: break-word;">{"tables": ["users", "orders", "products"]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML-formatted result suitable for rendering in dashboards or reports. Present when output_format is html.</td><td style="word-wrap: break-word;"><h3>Tables in Database: analytics_db</h3><pre>users orders products</pre></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Binary Excel content representing the result. Present when output_format is xlsx.</td><td style="word-wrap: break-word;"><binary-xlsx-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">Binary PDF content representing the result. Present when output_format is pdf.</td><td style="word-wrap: break-word;"><binary-pdf-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Action behavior: discover_tables lists tables in target_database; get_table_columns requires table_name; suggest_joins requires table_name and inspects foreign keys to propose JOIN statements; build_query provides template SQL patterns based on query_requirements.
- Only the selected output_format is populated; other outputs will be empty. Choosing text or all ensures a readable summary and JSON are returned.
- Valid MySQL credentials and sufficient permissions are required to list tables, read information_schema, and access table metadata.
- Join suggestions rely on information_schema.KEY_COLUMN_USAGE and will only be available when foreign key metadata exists and is accessible.
- Large datasets are not fetched by this helper; it focuses on discovery and guidance. Use query execution nodes to run actual queries.

## Troubleshooting
- Error: Table name is required for this action — Provide table_name when using get_table_columns or suggest_joins.
- No foreign key relationships found — The target table may not have defined FKs, or your user lacks metadata permissions. Verify schema constraints and privileges.
- Access denied or permission errors — Ensure the credentials have rights to read information_schema and the target database.
- Empty tables list — Confirm target_database is correct and the user can list tables in it.
- Output is empty in certain fields — This is expected when output_format is not set to that type. Select the appropriate output_format or choose all.
