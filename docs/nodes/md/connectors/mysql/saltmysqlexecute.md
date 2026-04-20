# MySQL Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node runs non-SELECT SQL statements (INSERT, UPDATE, DELETE, and other DML) against a MySQL database using stored credentials. It sends the SQL string to the MySQL service’s /execute endpoint and returns a formatted text summary along with structured JSON results. It is optimized for write operations and does not return full result tables like a query-focused node.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to modify data in a MySQL database from a Salt workflow—for example, inserting new user records, updating order statuses, or cleaning up stale rows. It typically appears after nodes that generate or validate data (such as text-to-JSON transformers, API fetch nodes, or business-logic nodes) and before reporting or logging steps that confirm the operation outcome. Upstream, you’ll often pair it with nodes that supply credentials (for example, a credentials path provider) and dynamic SQL generators. Downstream, common consumers are nodes that log results, send notifications, or branch logic based on success or failure (such as conditional or messaging or integration nodes). For read-only operations, use the "SaltMySQLQuery" node, and to inspect schema or structure use "SaltMySQLTableInfo". When integrating into larger workflows, keep write operations consolidated through this node so you can centrally manage timeouts, error handling, and auditing.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored MySQL credentials that match the "mysql" credential template. These credentials must include host, port, database, username, and password (or an equivalent connection string) that the MySQL service can use.</td><td style="word-wrap: break-word;">/run/secrets/mysql/prod-db.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the MySQL service to complete the execute request before treating it as a failure. Use higher values for long-running batch updates, but avoid excessively large timeouts in interactive pipelines.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A SQL INSERT, UPDATE, DELETE, or other non-SELECT statement to execute against the target MySQL database. Multi-line content is supported. The statement must be valid for your MySQL version. Avoid directly embedding untrusted user input; sanitize or parameterize upstream to prevent SQL injection.</td><td style="word-wrap: break-word;">UPDATE orders SET status = 'shipped', shipped_at = NOW() WHERE status = 'paid' AND shipped_at IS NULL LIMIT 500</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the execution result, suitable for display or logging. It typically includes a short title (for example, "MySQL Execute Results") and key outcome details such as affected row count or high-level status.</td><td style="word-wrap: break-word;">MySQL Execute Results: 124 rows affected by UPDATE orders SET status = 'shipped' WHERE status = 'paid' AND shipped_at IS NULL LIMIT 500</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured JSON payload returned by the MySQL execute endpoint. Common fields may include success flags, affected_rows, execution_time, and error details if the operation fails. Downstream nodes can parse this object for branching, auditing, or reporting.</td><td style="word-wrap: break-word;">{'success': True, 'query': "UPDATE orders SET status = 'shipped' WHERE status = 'paid' AND shipped_at IS NULL LIMIT 500", 'affected_rows': 124, 'execution_time_ms': 87, 'warnings': []}</td></tr>
<tr><td style="word-wrap: break-word;">table</td><td style="word-wrap: break-word;">DATAFRAME</td><td style="word-wrap: break-word;">Tabular representation of the JSON result, usually a small summary table rather than a large result set. This is mainly for compatibility with nodes that expect table-like inputs and may contain columns such as success, affected_rows, execution_time_ms, and error_message.</td><td style="word-wrap: break-word;">[{'success': True, 'affected_rows': 124, 'execution_time_ms': 87, 'error_message': None}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Large bulk updates or deletes can be slow and may lock tables; consider adding indexes, batching your statements, or limiting the rows per execution to keep runtimes predictable.
- **Limitations**: This node is designed for non-SELECT operations; use "SaltMySQLQuery" for read queries to avoid confusion about missing row data in the outputs.
- **Behavior**: The node sends the SQL text directly to the MySQL service without automatic sanitization or parameter binding. Always handle escaping and validation of any dynamic input in upstream nodes.
- **Behavior**: The exact structure of the JSON and table outputs depends on the underlying MySQL service; inspect the outputs in your environment and design downstream logic to tolerate optional or additional fields.

## Troubleshooting
- **Authentication or connection errors**: If you see errors about invalid credentials or connection refusal, verify that `credentials_path` points to a valid credential file or secret matching the `mysql` template and that the MySQL instance is reachable from the Salt environment.
- **SQL syntax or permission errors**: Messages like "You have an error in your SQL syntax" or "UPDATE command denied" indicate issues with `sql_text` or database permissions. Test the statement in a MySQL client and ensure your user has the required privileges.
- **Timeout exceeded**: If the node fails due to timeout, either optimize the SQL (add indexes, reduce affected rows, or break into smaller batches) or increase the `timeout` input to a level appropriate for the workload.
- **Unexpected zero affected rows**: When the result shows `affected_rows = 0` but changes were expected, double-check WHERE conditions, confirm the target database or schema from your credentials, and ensure there are matching rows and that triggers or constraints are not preventing updates.
