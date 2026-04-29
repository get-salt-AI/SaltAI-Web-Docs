# PostgreSQL Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node sends a non-SELECT SQL statement (INSERT, UPDATE, DELETE, or other DML) to a PostgreSQL database through a configured PostgreSQL service. It loads credentials from a secure credentials path, calls the underlying PostgreSQL execute endpoint, and returns a formatted summary of the execution result as text and JSON. Use it to modify data rather than retrieve result sets.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltPostgresExecute whenever you need to change data in a PostgreSQL database from a Salt workflow—for example inserting new records, updating fields, or deleting rows based on logic computed earlier in the pipeline. Typically, this node appears after business-logic or data-preparation nodes (such as LLM decision nodes, data transformation nodes, or SaltPostgresQuery results that inform what should be changed), and before reporting or notification nodes that confirm the operation. Upstream, you must supply a valid credentials path (usually from a credentials or environment configuration node) and set an appropriate timeout based on expected query cost. You can construct the SQL statement dynamically using outputs from previous nodes, then pass the final SQL string into this node. Downstream, nodes commonly parse or display the execution summary (rows affected, success/failure) for logging, user feedback, or conditional branching in the workflow. It pairs naturally with "SaltPostgresQuery" (for reading data), generic orchestration nodes that build SQL strings, and monitoring/logging nodes that record database changes. Prefer this node for write operations, and keep read operations separated in "SaltPostgresQuery" to maintain clear workflows and access patterns.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filesystem or configuration path pointing to stored PostgreSQL credentials that match the "postgres" credential template. This must resolve to connection details (host, port, database, user, and password or equivalent secure token) that the node can load before executing the statement.</td><td style="word-wrap: break-word;">/app/config/credentials/postgres/prod.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the PostgreSQL execute request to complete before failing. Use a slightly higher timeout for heavier bulk operations and a lower value for quick, latency-sensitive requests.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL INSERT, UPDATE, DELETE, or other non-SELECT statement to execute against the PostgreSQL database. Must be valid PostgreSQL syntax and must not require interactive parameters; build the final statement before passing it in. Multi-line statements are supported.</td><td style="word-wrap: break-word;">UPDATE customers SET status = 'inactive' WHERE last_login < NOW() - INTERVAL '365 days';</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the PostgreSQL execute operation, suitable for logs or UI display. Typically includes whether the statement succeeded and high-level info like number of affected rows or a generic success message.</td><td style="word-wrap: break-word;">PostgreSQL Execute Results: SUCCESS. 128 rows affected.</td></tr>
<tr><td style="word-wrap: break-word;">dataframe</td><td style="word-wrap: break-word;">DATAFRAME</td><td style="word-wrap: break-word;">Structured tabular representation of the execution response, usually containing metadata fields such as status and rows_affected. This is primarily useful for downstream nodes that expect table-like data even for non-SELECT operations.</td><td style="word-wrap: break-word;">{"columns": ["status", "rows_affected"], "data": [["success", 128]]}</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON payload returned from the PostgreSQL execute service endpoint, preserving all response fields. Use this when you need detailed metadata or to drive conditional branching based on low-level execution results.</td><td style="word-wrap: break-word;">{"status": "success", "rows_affected": 128, "query": "UPDATE customers SET status = 'inactive' WHERE last_login < NOW() - INTERVAL '365 days';"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Execution time depends on the complexity of the SQL statement and the volume of affected rows; large bulk updates or deletes may require higher timeouts and can impact database performance.
- **Limitations**: Designed for non-SELECT operations; for SELECT queries and retrieving result sets, use "SaltPostgresQuery" instead to avoid unexpected or empty tabular outputs.
- **Behavior**: The node delegates to a remote PostgreSQL service using the "postgres" credential template; incorrect or missing credentials_path values will cause authentication or connection failures before any SQL is executed.
- **Behavior**: SQL is sent as-is to the database; the node does not sanitize or parameterize statements, so you must handle SQL injection protection and input validation upstream.

## Troubleshooting
- **Error: Authentication failed or could not connect to database**: Verify that "credentials_path" points to a valid credentials file, the file matches the "postgres" template, and the network/security groups allow connections to the target PostgreSQL instance.
- **Error: Syntax error at or near ...**: The PostgreSQL server rejected the SQL statement; inspect "sql_text" for PostgreSQL-specific syntax issues, stray quotes, missing semicolons, or using a SELECT statement where this execute endpoint expects non-SELECT statements.
- **Timeout exceeded while executing request**: Your operation took longer than the configured "timeout"; increase the timeout value, simplify the query (for example, batch updates in smaller chunks), or ensure database indexes and performance are adequate.
- **No rows affected when expected**: The node executed successfully but did not change data; confirm that the WHERE clause conditions are correct and that the query is pointed at the intended database/schema via the credentials configuration.
