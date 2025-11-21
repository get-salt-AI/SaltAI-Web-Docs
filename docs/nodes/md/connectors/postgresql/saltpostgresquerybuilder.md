# PostgreSQL Query Builder Helper

Interactive helper for exploring a PostgreSQL database and scaffolding queries. It can list tables in a schema, inspect table columns, suggest JOINs based on foreign key relationships, and provide basic query-building guidance from a natural language description. Outputs can be returned as text/JSON and optionally as HTML, XLSX, or PDF summaries.

## Usage

Use this node early in a workflow to understand the database structure before writing queries. Start with discover_tables to see what's available in a schema, then get_table_columns for details on a specific table. Use suggest_joins to identify likely JOIN conditions. If you're unsure how to approach a query, use build_query to get starter patterns and guidance. Pair the insights with downstream nodes that execute or visualize queries.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to PostgreSQL credentials configured for the 'postgres' service.</td><td style="word-wrap: break-word;">/configs/credentials/postgres.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Request timeout (in seconds) for metadata lookups and helper actions.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Helper action to perform: discover schemas/tables, inspect columns, suggest joins, or build a query outline.</td><td style="word-wrap: break-word;">discover_tables</td></tr>
<tr><td style="word-wrap: break-word;">target_schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database schema to explore or use as the context for discovery and suggestions.</td><td style="word-wrap: break-word;">public</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Specific table for column discovery or join suggestions. Required when action is get_table_columns or suggest_joins.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">query_requirements</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Natural language description of the data you want (used when action is build_query to provide starter patterns and guidance).</td><td style="word-wrap: break-word;">Find users with their latest order and total spend in the last 30 days</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Choose how results are returned: text (text + JSON only), html (HTML summary), xlsx (Excel), pdf (PDF), or all (text + JSON; other formats may be empty for helper outputs).</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the requested helper action (tables list, columns, join suggestions, or query guidance).</td><td style="word-wrap: break-word;">Tables in Schema: public - users - orders - products</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-serialized payload of the helper result (e.g., lists of tables/columns or an array of suggested JOIN strings).</td><td style="word-wrap: break-word;">{"suggestions": ["JOIN orders ON users.id = orders.user_id"]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML-formatted summary of the helper result when output_format is html.</td><td style="word-wrap: break-word;"><h3>Join Suggestions: public.users</h3><pre>JOIN orders ON users.id = orders.user_id</pre></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Excel file bytes containing a simple tabular representation of the helper result when output_format is xlsx.</td><td style="word-wrap: break-word;"><bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">PDF file bytes with a formatted report of the helper result when output_format is pdf.</td><td style="word-wrap: break-word;"><bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Action requirements**: table_name is required for get_table_columns and suggest_joins.
- **Schema context**: target_schema defaults to public. Ensure it matches your data.
- **Join suggestions**: Derived from information_schema foreign key relationships and may not capture all logical joins.
- **Build query**: Provides generic SQL patterns and guidance; it does not execute a query. Use execution or visual query nodes to run SQL.
- **Output population**: Depending on output_format, only some outputs will be populated; others may be empty strings.
- **Permissions**: The provided credentials must have sufficient privileges to read information_schema and target tables.

## Troubleshooting
- **Empty results when discovering tables**: Verify target_schema is correct and the user has permissions to list tables.
- **No join suggestions returned**: The table may not have foreign key relationships, or permissions to information_schema are limited.
- **Column discovery errors**: Ensure table_name is provided and spelled correctly, and that the table exists in target_schema.
- **Timeouts**: Increase the timeout input for large schemas or slow connections.
- **Unexpected empty XLSX/PDF**: For helper actions, only summary content is generated; choose text or html for richer on-screen results, or ensure output_format matches your needs.
- **Authentication failures**: Check credentials_path configuration, host accessibility, and that the credentials are valid for the 'postgres' service.
