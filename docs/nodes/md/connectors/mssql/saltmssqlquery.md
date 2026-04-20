# SQL Server Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltMSSQLQuery executes read-only SQL Server (MSSQL) SELECT statements using credentials defined under the MSSQL credential template. It loads the MSSQL connection details, runs the query with a timeout, and outputs the result set in a structured format. The node is focused on querying data and handing it off to downstream steps for analysis, transformation, or display.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqlquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltMSSQLQuery when your Salt workflow needs to read data from a Microsoft SQL Server database, such as retrieving customer records, order histories, configuration tables, or analytic snapshots. Place it after a node or configuration step that provides a valid `credentials_path` for the MSSQL connection and any logic that sets an appropriate `timeout`. Connect its output to nodes that transform, summarize, enrich, or export the data (for example, text generation nodes, data transformation nodes, or export/integration nodes). It pairs naturally with SaltMSSQLExecute (for INSERT/UPDATE/DELETE and other write operations) and SaltMSSQLTableInfo (for exploring schemas and table metadata). Keep queries strictly read-only and scoped with WHERE filters and TOP limits to control performance. If parts of `sql_text` are constructed from user or external input, build the final query string safely in upstream steps to avoid SQL injection.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Absolute or workspace-relative path to a stored MSSQL credential configuration that conforms to the "mssql" credential template. This configuration must include the connection details (such as server host, port, database name, and authentication information). The path must be valid and readable by the Salt execution environment or the node will fail before any query is executed.</td><td style="word-wrap: break-word;">/workspace/credentials/mssql/production_sales_db.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds the node will wait for the SQL Server query to complete. If execution exceeds this duration, the node aborts and raises a timeout error. Use smaller values for simple, interactive queries and larger ones for complex or long-running analytical queries, taking into account database load and resource constraints.</td><td style="word-wrap: break-word;">25</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL Server SELECT statement to execute. This multiline string input is intended only for read operations and must not include INSERT, UPDATE, DELETE, or DDL commands. MSSQL-specific syntax such as SELECT TOP, JOINs, aggregates, and ORDER BY is supported. If any portion of this query is derived from user or external inputs, construct the final SQL safely in upstream nodes to avoid SQL injection vulnerabilities.</td><td style="word-wrap: break-word;">SELECT TOP 100 customer_id, full_name, region, created_at FROM dbo.Customers WHERE region = 'EMEA' AND created_at >= '2024-01-01' ORDER BY created_at DESC</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured MSSQL query result data produced by the underlying database base node. This typically includes column metadata and an array of rows, where each row is a key-value mapping from column names to their values, along with optional summary information such as row count. Downstream nodes can filter or aggregate these rows, convert them into natural language summaries, join them with other datasets, or export them to external systems.</td><td style="word-wrap: break-word;">{'columns': [{'name': 'customer_id', 'type': 'INT'}, {'name': 'full_name', 'type': 'NVARCHAR'}, {'name': 'region', 'type': 'NVARCHAR'}, {'name': 'created_at', 'type': 'DATETIME2'}], 'rows': [{'customer_id': 20015, 'full_name': 'Jane Doe', 'region': 'EMEA', 'created_at': '2024-02-10T09:32:14'}, {'customer_id': 20016, 'full_name': 'Carlos Alvarez', 'region': 'EMEA', 'created_at': '2024-02-10T09:45:02'}], 'row_count': 2}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Unbounded SELECT queries without TOP or restrictive WHERE clauses can return very large datasets, causing long runtimes, high memory usage, and increased risk of timeouts. Always limit or filter results where possible.
- **Limitations**: This node is for SELECT/read-only operations. For data modification or schema changes (INSERT, UPDATE, DELETE, DDL), use SaltMSSQLExecute instead of SaltMSSQLQuery.
- **Behavior**: The node depends on MSSQL credentials configured under the "mssql" credential template. Incorrect host, port, database name, or authentication details in the credentials file will cause connection or authorization failures before any SQL is executed.
- **Behavior**: The SQL string is executed as provided, with no automatic parameter binding or escaping. If you incorporate user or dynamic values into `sql_text`, you must handle validation and sanitization in upstream logic to prevent SQL injection vulnerabilities.
- **Performance**: The `timeout` input applies to the overall query execution. Under heavy database load or with complex queries, you may need to increase the timeout or optimize SQL and indexing to prevent recurring timeout errors.

## Troubleshooting
- **Authentication or connection error**: If you see messages about login failure, unknown host, or connection refused, verify that `credentials_path` is correct, that the credential file contains valid MSSQL server details, and that the Salt environment can reach the database over the network.
- **Query timeout exceeded**: When the node fails with a timeout error, either increase the `timeout` value or optimize the query by refining WHERE clauses, reducing joins or selected columns, and using TOP to limit the number of returned rows.
- **SQL syntax error near ...**: Errors indicating incorrect or unexpected syntax point to issues in `sql_text`. Test the query directly in a SQL Server client against the same database, correct any syntax or identifier problems (such as table or column names and quoting), and then update the node configuration.
- **Empty or unexpected result set**: If the node runs successfully but returns no rows or missing columns, verify the WHERE conditions, confirm that the correct database and schema are referenced in both the credentials and the query, and ensure that the database user has SELECT permissions on the relevant tables and columns.
