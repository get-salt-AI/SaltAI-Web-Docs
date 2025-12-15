# PostgreSQL Query Builder Helper

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Interactive helper for exploring a PostgreSQL database and preparing queries. It can list tables in a schema, show columns for a table, suggest JOINs based on foreign key relationships, and provide baseline query-building guidance from a natural-language description. Results can be returned as text/JSON and optionally as HTML, XLSX, or PDF depending on the selected output format.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresquerybuilder.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node early in a workflow to understand your database structure before building full SQL. Typical flow: 1) discover_tables to list tables in a schema, 2) get_table_columns to inspect column details, 3) suggest_joins for relationship hints, 4) build_query to get high-level guidance from a description, then pass your findings to a query execution or visual query node to run the final SQL.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored PostgreSQL credentials compatible with the 'postgres' credential template.</td><td style="word-wrap: break-word;">/configs/credentials/postgres.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the request to complete.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Operation to perform. Options: discover_tables, get_table_columns, suggest_joins, build_query.</td><td style="word-wrap: break-word;">discover_tables</td></tr>
<tr><td style="word-wrap: break-word;">target_schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema to explore for tables and relationships.</td><td style="word-wrap: break-word;">public</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Table name used for column discovery or join suggestions. Required for get_table_columns and suggest_joins.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">query_requirements</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Natural-language description of the data you want, used to provide basic query-building suggestions when action is build_query.</td><td style="word-wrap: break-word;">Find each user with their total number of orders in the last 30 days</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Result format. Options: text, html, xlsx, pdf, all.</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable text summary of the action result (tables list, column info, join suggestions, or guidance). Empty for some formats.</td><td style="word-wrap: break-word;">Tables in Schema: public\n- users\n- orders\n...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the structured result or error details.</td><td style="word-wrap: break-word;">{"suggestions": ["JOIN orders ON users.id = orders.user_id"]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML representation of the result when output_format is html. Otherwise empty.</td><td style="word-wrap: break-word;"><h3>Join Suggestions: public.users</h3><pre>JOIN orders ON users.id = orders.user_id</pre></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">XLSX binary data of the result when output_format is xlsx. Otherwise empty.</td><td style="word-wrap: break-word;"><xlsx-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF binary data of the result when output_format is pdf. Otherwise empty.</td><td style="word-wrap: break-word;"><pdf-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Table name is required when action is get_table_columns or suggest_joins.
- discover_tables and get_table_columns rely on valid credentials and access to the target schema.
- suggest_joins inspects information_schema metadata to infer foreign key relationships; results depend on proper FK constraints in the database.
- build_query returns general patterns and suggestions; it does not generate executable SQL automatically.
- When output_format is html/xlsx/pdf, text may be empty and only the selected format plus JSON is returned.
- For 'all' output in this helper, text and JSON are returned; HTML/XLSX/PDF are empty for helper actions.

## Troubleshooting
- Error: 'Table name is required for column discovery/join suggestions' — Provide a non-empty table_name when using get_table_columns or suggest_joins.
- No tables returned — Verify target_schema is correct and the credentials have sufficient privileges.
- No foreign key relationships found — Ensure FK constraints exist; otherwise, join suggestions may be empty.
- Timeout occurred — Increase the timeout value or narrow the scope (e.g., use a specific schema or table).
- Unexpected result format — Confirm output_format matches your desired output; switch to 'text' for quick summaries or 'html/xlsx/pdf' for formatted outputs.
