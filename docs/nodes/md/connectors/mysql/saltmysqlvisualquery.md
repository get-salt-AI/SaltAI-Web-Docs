# MySQL Visual Query Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node lets you construct complex MySQL SELECT queries using structured parameters instead of writing raw SQL. You configure the main table, join definitions, WHERE/GROUP BY/HAVING/ORDER BY clauses, and pagination, and the node assembles a single SQL statement from these parts. It then executes the query through the configured MySQL service and returns results as text and JSON, with optional HTML, Excel, or PDF exports for downstream use.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlvisualquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need multi-table or advanced MySQL queries but want to define them via parameters rather than raw SQL strings. Place it after a credentials/config node that provides credentials_path and before nodes that consume tabular or JSON data (for analytics, reporting, or AI processing). A typical pattern is: validate connection with SaltMySQLTestConnection, explore structure with SaltMySQLListDatabases, SaltMySQLListTables, and SaltMySQLTableInfo, discover join options using SaltMySQLQueryBuilder (for suggest_joins output), then configure SaltMySQLVisualQuery with the chosen main_table, database, and join_config. For simple one-table selects, SaltMySQLQuery is simpler; for inserts/updates/deletes, use SaltMySQLExecute. Choose output_format based on integration: text/json for programmatic pipelines and AI, html for dashboards and emails, xlsx or pdf for reporting and exports.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reference path or identifier of the stored MySQL credential set that uses the mysql credential template. The node loads this to authenticate to the MySQL backend and does not take raw passwords directly.</td><td style="word-wrap: break-word;">connections/prod/mysql_reporting_cluster</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of seconds to wait for the query execution before failing. Helps keep flows responsive when queries are slow or blocked.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">main_table</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the primary table used in the FROM clause. Most joins and conditions will be built around this table.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the database (schema) containing the main table. Used to fully qualify the table in SQL as database.main_table.</td><td style="word-wrap: break-word;">customer_warehouse</td></tr>
<tr><td style="word-wrap: break-word;">selected_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of columns or expressions to include in the SELECT clause. Use * to select all columns, or list explicit columns (preferably fully qualified) to control the shape of the result.</td><td style="word-wrap: break-word;">users.id, users.email, orders.id AS order_id, orders.total_amount, payments.status</td></tr>
<tr><td style="word-wrap: break-word;">join_config</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array describing the JOINs to apply. Each element must be an object with keys: type (one of INNER, LEFT, RIGHT, FULL, CROSS), table (joined table name), and on (join condition). An optional database field can be used per join to specify another schema; if omitted, a default is applied in code. The node validates that this is valid JSON, that it is a list, and that each join entry has the required keys.</td><td style="word-wrap: break-word;">[{"type": "LEFT", "table": "orders", "database": "customer_warehouse", "on": "users.id = orders.user_id"}, {"type": "INNER", "table": "payments", "on": "orders.id = payments.order_id"}]</td></tr>
<tr><td style="word-wrap: break-word;">where_conditions</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL conditions for the WHERE clause, without the keyword WHERE. Leave empty to omit the WHERE clause. Must be a valid MySQL boolean expression referencing columns from the main and joined tables.</td><td style="word-wrap: break-word;">users.signup_date >= '2024-01-01' AND users.country = 'US' AND orders.status = 'completed'</td></tr>
<tr><td style="word-wrap: break-word;">group_by_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated column list for the GROUP BY clause, without the keyword GROUP BY. Leave empty if no aggregation is needed. When set, ensure your SELECT includes aggregate functions or these grouping keys.</td><td style="word-wrap: break-word;">users.country, DATE(orders.created_at)</td></tr>
<tr><td style="word-wrap: break-word;">having_conditions</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL conditions for the HAVING clause, without the keyword HAVING. Only used if group_by_columns is non-empty. Typically contains filters on aggregate expressions like SUM, COUNT, AVG.</td><td style="word-wrap: break-word;">COUNT(orders.id) >= 5 AND SUM(orders.total_amount) > 500</td></tr>
<tr><td style="word-wrap: break-word;">order_by_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">ORDER BY specification as a comma-separated list, without the keyword ORDER BY. You can specify direction per column using ASC or DESC.</td><td style="word-wrap: break-word;">users.signup_date DESC, orders.total_amount DESC</td></tr>
<tr><td style="word-wrap: break-word;">limit_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of rows to return. Must be between 1 and 10000. A LIMIT clause is added whenever this is greater than zero.</td><td style="word-wrap: break-word;">500</td></tr>
<tr><td style="word-wrap: break-word;">offset_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of rows to skip before returning results. Must be between 0 and 10000. When greater than zero, an OFFSET clause is appended after LIMIT.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">CHOICE[text\|html\|xlsx\|pdf\|all]</td><td style="word-wrap: break-word;">Determines how the node formats its outputs. text returns human-readable text plus JSON; html returns an HTML table; xlsx returns Excel binary data; pdf returns PDF binary data; all returns text and JSON along with all exportable formats.</td><td style="word-wrap: break-word;">all</td></tr>
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
<tr><td style="word-wrap: break-word;">text_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable formatted representation of the query results or an error message. For successful runs this usually contains a header with row count and a row-by-row breakdown; for export-only formats it may be empty.</td><td style="word-wrap: break-word;">Visual Query Results (2 rows): ================================================== Row 1:   id: 10293   email: alice@example.com   order_id: 55001   total_amount: 249.99   country: US  Row 2:   id: 10566   email: bob@example.com   order_id: 55002   total_amount: 139.50   country: US</td></tr>
<tr><td style="word-wrap: break-word;">json_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the raw result payload from the MySQL service. Typically includes a data array with row objects and may include fields like row_count.</td><td style="word-wrap: break-word;">{"data": [{"id": 10293, "email": "alice@example.com", "order_id": 55001, "total_amount": 249.99, "country": "US"}, {"id": 10566, "email": "bob@example.com", "order_id": 55002, "total_amount": 139.5, "country": "US"}], "row_count": 2}</td></tr>
<tr><td style="word-wrap: break-word;">html_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML markup containing a table of results when output_format is html or all. Can be embedded into web views, dashboards, or rich emails.</td><td style="word-wrap: break-word;"><h3>Visual Query: customer_warehouse.users</h3><table><thead><tr><th>id</th><th>email</th><th>order_id</th><th>total_amount</th><th>country</th></tr></thead><tbody><tr><td>10293</td><td>alice@example.com</td><td>55001</td><td>249.99</td><td>US</td></tr><tr><td>10566</td><td>bob@example.com</td><td>55002</td><td>139.50</td><td>US</td></tr></tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_output</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary Excel (.xlsx) data with the query results when output_format is xlsx or all. Intended to be saved to storage or sent as a file attachment.</td><td style="word-wrap: break-word;">Binary XLSX data stream representing the result rows in a worksheet named Results.</td></tr>
<tr><td style="word-wrap: break-word;">pdf_output</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF data with a tabular representation of the query results when output_format is pdf or all. Useful for printable reports and archiving.</td><td style="word-wrap: break-word;">Binary PDF data stream containing a report titled Visual Query: customer_warehouse.users and the result table.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node always generates a full SQL statement and sends it to the backend; broad scans of large tables or very high LIMIT/OFFSET values can be slow and may hit the timeout or backend resource limits even though LIMIT is capped at 10,000 rows.
- **Limitations**: join_config must be a valid JSON list of objects, each with type, table, and on. Invalid JSON, wrong top-level type, or missing fields will raise parsing or validation errors before any database call is made.
- **Behavior**: The WHERE, GROUP BY, HAVING, and ORDER BY segments are inserted verbatim into the SQL. The node does not sanitize or rewrite them, so syntax errors or unsafe expressions will result in MySQL errors and should be controlled by how you build these strings.
- **Behavior**: On errors (such as invalid join config, unsupported join type, JSON parsing issues, or backend SQL errors), the node returns an error message as text_output and a JSON object with an error field in json_output, while HTML/XLSX/PDF outputs may be empty regardless of the selected output_format.
- **Integration**: Export formats (HTML, XLSX, PDF) rely on the backend returning a data array of records; if the service response shape changes, raw JSON may still be available but these formatted outputs can degrade or fail.

## Troubleshooting
- **Invalid JSON in join configuration: ...**: This occurs when join_config is not valid JSON (for example, using single quotes, missing brackets, or trailing commas). Fix it by ensuring the value is a JSON array string with double quotes and well-formed objects, or generate it from a helper or configuration UI.
- **Error parsing join configuration: Join N missing required field: type/table/on**: Each join entry must be an object containing at least type, table, and on. Inspect the join at the reported index and add any missing fields or correct key names.
- **Invalid join type: ... Must be one of: INNER, LEFT, RIGHT, FULL, CROSS**: The type field is validated against a fixed set of values. Replace variants like left_outer or inner join with LEFT or INNER and adjust your join_config accordingly.
- **Visual query execution failed: ...**: This generic message wraps underlying SQL or connection errors, such as syntax errors in WHERE/HAVING, missing columns or tables, permission issues, or timeouts. Review the error detail, test the corresponding SQL in a MySQL client, simplify clauses, or increase timeout if the query is legitimately heavy.
- **No data returned from visual query**: If the node completes successfully but returns no rows, first relax or remove where_conditions and having_conditions to ensure data exists, check that main_table, database, and joins match your schema, and use SaltMySQLQueryBuilder discovery actions to verify table and column names.
