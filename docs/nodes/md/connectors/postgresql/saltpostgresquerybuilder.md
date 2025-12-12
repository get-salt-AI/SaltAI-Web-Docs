# PostgreSQL Query Builder Helper

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Helps you explore a PostgreSQL database and build queries interactively. It can list tables in a schema, show a table’s columns, suggest JOINs based on detected relationships, and provide SQL-building guidance from a natural language description. Results can be returned as plain text + JSON, or rendered/exported as HTML, XLSX, or PDF.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresquerybuilder.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a data workflow to discover available tables and their columns, or to get JOIN suggestions before constructing complex queries. It’s also useful when you have a plain-language requirement and want structured guidance on how to translate it into SQL. Connect it to nodes that will execute queries or render data, selecting the appropriate output format for your needs.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to PostgreSQL credentials configured for the postgres service.</td><td style="word-wrap: break-word;">/path/to/credentials/postgres.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the helper operations to complete.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Operation to perform. Options: discover_tables, get_table_columns, suggest_joins, build_query.</td><td style="word-wrap: break-word;">discover_tables</td></tr>
<tr><td style="word-wrap: break-word;">target_schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema to explore or to use as the default context for actions.</td><td style="word-wrap: break-word;">public</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Table name for column discovery or join suggestions. Required if action is get_table_columns or suggest_joins.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">query_requirements</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Natural language description of the data you want. Used when action is build_query to provide SQL guidance.</td><td style="word-wrap: break-word;">List each user and the total count of their orders in the last 30 days</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Output format for results. Options: text, html, xlsx, pdf, all.</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the helper result (tables, columns, suggested joins, or query guidance). Empty string if not returned for the selected format.</td><td style="word-wrap: break-word;">Tables in Schema: public users orders ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Machine-readable JSON representation of the result (e.g., lists, suggestions, or structured guidance).</td><td style="word-wrap: break-word;">{"suggestions": ["JOIN orders ON users.id = orders.user_id"], "requirements": "List each user and total orders"}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML rendering of the result. Returned only when output_format is html; otherwise empty.</td><td style="word-wrap: break-word;"><h3>Join Suggestions: public.users</h3><pre>JOIN orders ON users.id = orders.user_id</pre></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Excel data representing the result. Returned only when output_format is xlsx; otherwise empty.</td><td style="word-wrap: break-word;"><binary xlsx bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF document representing the result. Returned only when output_format is pdf; otherwise empty.</td><td style="word-wrap: break-word;"><binary pdf bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Table name is required when action is get_table_columns or suggest_joins.
- Schema defaults to "public" if not provided.
- JOIN suggestions rely on database metadata; they may be limited or empty if foreign keys are not defined or accessible.
- The node requires valid PostgreSQL credentials configured for the postgres service via credentials_path.
- Selecting html, xlsx, or pdf returns that specific format alongside JSON, with other formats left empty. Selecting all returns text and JSON (helper results do not bundle all export types).
- Timeout applies to the helper operations (e.g., listing tables, fetching columns, suggesting joins).

## Troubleshooting
- Action requires a table_name: If you choose get_table_columns or suggest_joins without a table_name, supply a valid table name and retry.
- No tables or columns returned: Verify target_schema exists and your credentials have permissions to read metadata.
- No JOIN suggestions shown: Ensure foreign key relationships exist and your credentials can access system metadata. If none are defined, suggestions may be empty.
- Permission errors: Confirm the credentials_path points to valid credentials with sufficient privileges for the target schema.
- Empty or unexpected outputs in selected format: Switch output_format to text to inspect the plain-text and JSON details for troubleshooting.
