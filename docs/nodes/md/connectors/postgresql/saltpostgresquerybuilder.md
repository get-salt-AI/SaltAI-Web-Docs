# PostgreSQL Query Builder Helper

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Interactive helper for exploring a PostgreSQL schema and drafting queries. It can list tables, show a table’s columns, suggest JOIN relationships based on foreign keys, and provide starter SQL patterns from a natural-language requirement. Results can be returned as text/JSON, or exported as simple HTML/XLSX/PDF summaries.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresquerybuilder.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node early in your database workflow to understand schema structure and plan queries. Typical flow: start with discover_tables to see what’s available, then get_table_columns for details on a specific table, use suggest_joins to identify relationships, and finally build_query to generate starter query ideas from a plain-English description. Pair it with a query execution or visual query node to run or refine the SQL.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database connection URI for PostgreSQL.</td><td style="word-wrap: break-word;">postgresql://<username>:<password>@<host>:5432/<database>?schema=public</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Request timeout in seconds for service operations.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">What helper action to perform.</td><td style="word-wrap: break-word;">discover_tables</td></tr>
<tr><td style="word-wrap: break-word;">target_schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema to explore for tables, columns, and relationships.</td><td style="word-wrap: break-word;">public</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Table name used when action is get_table_columns or suggest_joins.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">query_requirements</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Natural-language description of the data you want, used when action is build_query.</td><td style="word-wrap: break-word;">List users and their orders with total amounts this month</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">Choose how the helper results are returned or exported.</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary text of the helper result (tables, columns, join suggestions, or query patterns).</td><td style="word-wrap: break-word;">Tables in schema 'public' (12 tables): - users - orders - products ...</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the structured result (e.g., lists of tables, column metadata, or join suggestions).</td><td style="word-wrap: break-word;">{"tables": ["users", "orders", "products"]}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML content containing a simple formatted view of the helper result (populated when output_format=html).</td><td style="word-wrap: break-word;"><!DOCTYPE html><html>...<table>...</table></html></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX file with a simple export of the helper result (populated when output_format=xlsx).</td><td style="word-wrap: break-word;"><base64-xlsx-data></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF with a simple export of the helper result (populated when output_format=pdf).</td><td style="word-wrap: break-word;"><base64-pdf-data></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Table name is required when using get_table_columns or suggest_joins; otherwise, an error is returned.
- build_query provides template suggestions based on your description; it does not generate or execute a final SQL statement.
- discover_tables and get_table_columns rely on the connected database’s metadata; ensure permissions grant access to information_schema/catalogs.
- output_format controls which of the export fields are populated. For html/xlsx/pdf, only that format (plus JSON) is filled; selecting all is not supported for this helper and will return primarily text and JSON.
- The node requires a valid PostgreSQL URI in credentials_path. Invalid or unsupported URI formats will cause validation errors.
- XLSX and PDF outputs are base64-encoded strings intended for downstream save/export nodes.

## Troubleshooting
- Invalid credentials URI: Ensure credentials_path uses a valid PostgreSQL URI format like postgresql://<username>:<password>@<host>:5432/<database>?schema=public
- Table name required: For get_table_columns or suggest_joins, provide table_name; otherwise the node returns an error.
- No foreign key relationships found: suggest_joins may return a message indicating none were found. Verify constraints exist or check the correct schema.
- Timeouts or connection errors: Increase timeout, verify network connectivity, and confirm the database service is reachable with your credentials.
- Empty or minimal output in html/xlsx/pdf: These exports summarize the helper results; they are not full query result sets. For data exports, run the actual query with a query execution node.
