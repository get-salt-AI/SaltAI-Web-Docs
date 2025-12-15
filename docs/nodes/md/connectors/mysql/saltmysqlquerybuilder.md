# MySQL Query Builder Helper

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Assists with interactive MySQL query building and schema discovery. It can list tables, fetch table columns, suggest possible JOINs using foreign key relationships, and generate starter query suggestions from a natural language description. Results can be returned as text/JSON or exported to HTML, XLSX, or PDF, depending on the chosen output format.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlquerybuilder.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to explore a MySQL database and iteratively build queries without writing SQL from scratch. Typical workflow: first discover_tables to see available tables, then get_table_columns for structure, use suggest_joins to identify relationships, and finally build_query to get example SQL patterns based on your requirements. Provide valid MySQL credentials and select an output_format appropriate for your downstream nodes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored MySQL credentials to authenticate requests.</td><td style="word-wrap: break-word;">/credentials/mysql/default.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the database service to respond.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">What to do: discover tables, get table columns, suggest joins, or build a query from a description.</td><td style="word-wrap: break-word;">discover_tables</td></tr>
<tr><td style="word-wrap: break-word;">target_database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The database (schema) to explore.</td><td style="word-wrap: break-word;">sales_db</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target table when action is get_table_columns or suggest_joins. Ignored for discover_tables and build_query.</td><td style="word-wrap: break-word;">orders</td></tr>
<tr><td style="word-wrap: break-word;">query_requirements</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Natural language description of what you want to retrieve. Used when action is build_query.</td><td style="word-wrap: break-word;">List customers and their total order amounts in the last 30 days, sorted by total descending</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Result format. text returns formatted text plus JSON; html returns an HTML block; xlsx returns an Excel file; pdf returns a PDF; all returns a combined set when applicable.</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary or suggestions, populated when output_format is text (and may be included for all in some cases).</td><td style="word-wrap: break-word;">Tables in Database: sales_db orders customers order_items</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Machine-readable JSON string with the raw data (e.g., tables, columns, or join suggestions).</td><td style="word-wrap: break-word;">{"suggestions": ["JOIN orders ON customers.id = orders.customer_id"]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML-rendered content (e.g., a formatted block with the result), populated when output_format is html.</td><td style="word-wrap: break-word;"><h3>Tables in Database: sales_db</h3><pre>orders\ncustomers</pre></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Excel file bytes containing the result, populated when output_format is xlsx.</td><td style="word-wrap: break-word;"><xlsx-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF file bytes containing the result, populated when output_format is pdf.</td><td style="word-wrap: break-word;"><pdf-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Table name is required when action is get_table_columns or suggest_joins; leave it empty for discover_tables or build_query.
- Join suggestions are inferred from foreign key relationships via information_schema; the database user must have permissions to read metadata.
- output_format controls which output ports are populated. For text, you’ll receive a formatted text plus a JSON string; for html/xlsx/pdf, the respective output will be filled along with JSON.
- Provide a valid credentials_path configured for MySQL; the node uses it to authenticate against the database service.
- The build_query action returns example SQL patterns and guidance derived from your query_requirements; it does not automatically execute SQL.

## Troubleshooting
- Action requires table_name: If you get an error using get_table_columns or suggest_joins, ensure table_name is set (e.g., "orders").
- No join suggestions returned: The table may not have foreign key relationships, or your user lacks metadata permissions on information_schema. Verify constraints and permissions.
- Empty discovery results: Confirm target_database exists and the credentials have access. Try discover_tables with a different database or check permissions.
- Timeouts or connection errors: Increase timeout, verify network access to the database service, and confirm credentials_path points to valid MySQL credentials.
- Invalid output format: Ensure output_format is one of text, html, xlsx, pdf, or all.
