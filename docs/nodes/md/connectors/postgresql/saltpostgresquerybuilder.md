# PostgreSQL Query Builder Helper

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node provides an interactive assistant for PostgreSQL query design. It can list tables in a schema, inspect table columns, suggest JOINs based on foreign key relationships, and generate example SQL patterns from a natural language description of your data needs. It is optimized as a companion to other PostgreSQL nodes, helping you understand your schema and design queries without deep SQL expertise.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresquerybuilder.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you are designing or refining PostgreSQL queries and need help understanding the database structure or crafting joins. Typical workflows: (1) Schema discovery: run with action=discover_tables and target_schema=public (or your schema) to enumerate tables before you query them, then follow with PostgreSQL Table Info or PostgreSQL Visual Query Builder nodes. (2) Column inspection: with action=get_table_columns, set table_name (for example, orders) and target_schema to retrieve column info for choosing select, filter, and join fields in downstream nodes like PostgreSQL Query or PostgreSQL Visual Query. (3) Join design: with action=suggest_joins, the node inspects foreign key metadata and returns JOIN clauses such as "JOIN orders ON users.id = orders.user_id" which you can paste into SQL or visual configuration. (4) Natural language query planning: with action=build_query, describe your requirement in query_requirements; the node returns example SQL patterns and guidance that you then adapt and execute via execution-focused PostgreSQL nodes. Place this helper upstream of nodes that actually run queries, and choose output_format (text, html, xlsx, pdf, all) depending on whether you need on-screen inspection, embeddable HTML, or shareable files.

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
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE[discover_tables,get_table_columns,suggest_joins,build_query]</td><td style="word-wrap: break-word;">The operation to perform. discover_tables lists tables in target_schema. get_table_columns returns column information for table_name in target_schema. suggest_joins examines foreign key relationships for table_name to propose JOIN clauses. build_query uses query_requirements to return generic SQL patterns and suggestions.</td><td style="word-wrap: break-word;">suggest_joins</td></tr>
<tr><td style="word-wrap: break-word;">target_schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database schema to explore. Used for table listing, column lookup, and join discovery. Must match an existing schema, such as public or an application-specific schema name.</td><td style="word-wrap: break-word;">public</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Table name for column discovery and join suggestions. Required when action is get_table_columns or suggest_joins. Must be a valid table within target_schema; otherwise, you will see errors or empty results.</td><td style="word-wrap: break-word;">orders</td></tr>
<tr><td style="word-wrap: break-word;">query_requirements</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Natural language description of what data you want to retrieve, used when action is build_query. The node does not execute queries from this; it returns patterns and guidance you can manually translate into SQL or configure in visual query nodes.</td><td style="word-wrap: break-word;">I need a list of active users and the total value of their orders in the last 90 days, grouped by user and sorted by total spent descending.</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">CHOICE[text,html,xlsx,pdf,all]</td><td style="word-wrap: break-word;">Format for the helper output. text returns a human-readable string plus JSON. html wraps the textual result in a simple HTML block. xlsx and pdf generate basic spreadsheet or PDF files containing the textual results. all behaves like text for this helper, primarily returning text and JSON while leaving export channels mostly empty.</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Plain-text representation of the helper result: tables list, column details summary, join suggestions, or query-building guidance. Intended for human review or for feeding into other text-processing components.</td><td style="word-wrap: break-word;">Join Suggestions for public.users: JOIN orders ON users.id = orders.user_id JOIN profiles ON users.id = profiles.user_id</td></tr>
<tr><td style="word-wrap: break-word;">json_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded string of the structured helper result. For join suggestions it includes a suggestions array; for query building it includes both suggestions and the original requirements; for discovery operations it encodes the discovered metadata.</td><td style="word-wrap: break-word;">{"suggestions": ["JOIN orders ON users.id = orders.user_id"], "requirements": "I want to find users and their orders"}</td></tr>
<tr><td style="word-wrap: break-word;">html_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML snippet containing the formatted helper result when output_format is html. Includes a heading with the title and a preformatted block with the text content. Empty string for non-HTML formats.</td><td style="word-wrap: break-word;"><h3>Join Suggestions: public.users</h3><pre>Join Suggestions for public.users: JOIN orders ON users.id = orders.user_id</pre></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_output</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary XLSX data when output_format is xlsx. The file typically contains a simple sheet with a single column (for example, Result) and the formatted text in one or more rows. Empty or unused when another format is selected.</td><td style="word-wrap: break-word;">Binary XLSX content representing a sheet with one column 'Result' and one row containing 'Join Suggestions for public.users: ...'</td></tr>
<tr><td style="word-wrap: break-word;">pdf_output</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF data when output_format is pdf. The PDF contains the helper text rendered on a page with a title corresponding to the operation (for example, Join Suggestions: public.users). Empty or unused when another format is selected.</td><td style="word-wrap: break-word;">Binary PDF content for a single-page document titled 'Join Suggestions: public.users' listing each JOIN suggestion on its own line.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The suggest_joins action queries information_schema to inspect foreign keys; on very large databases this can be slow. Increase timeout or narrow target_schema if operations time out.
- **Limitations**: The build_query action is a basic pattern generator and does not fully interpret complex natural language requirements into production-ready SQL. Always review and adapt the suggested patterns.
- **Behavior**: When action is get_table_columns or suggest_joins and table_name is empty, the node raises a clear validation error instead of guessing a table, preventing accidental introspection of unintended tables.
- **Behavior**: With output_format = all, this helper primarily returns text_output and json_output, while other outputs remain empty; full multi-format exports are more relevant for data-returning query nodes like PostgreSQL Visual Query.

## Troubleshooting
- **Missing table name error**: If you see messages like "Table name is required for column discovery" or "Table name is required for join suggestions", set table_name to a valid table when using get_table_columns or suggest_joins.
- **Unknown action selected**: An error such as "Unknown action: ..." indicates an invalid action string. Ensure action is exactly one of discover_tables, get_table_columns, suggest_joins, or build_query with no typos.
- **No join suggestions produced**: If the output says "No foreign key relationships found for table ...", the table may not have foreign key constraints or they exist in another schema. Verify your schema design and confirm target_schema is correct.
- **Empty or timeout results for discovery**: When discover_tables or get_table_columns returns empty data or a timeout-like error, check that credentials_path points to the intended database, that target_schema exists, and consider increasing timeout if the catalog is large.
