# PostgreSQL Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node sends a data-modifying SQL statement (INSERT, UPDATE, DELETE and similar) to a PostgreSQL database via the configured PostgreSQL service. It uses stored credentials to connect and forwards the SQL to the backend "/execute" endpoint. The node returns a human-readable text summary and a JSON payload describing the execution result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the PostgreSQL Execute node whenever your workflow needs to change data in a PostgreSQL database instead of reading it. Typical scenarios include inserting new records after an LLM or API call, updating user or order statuses as part of a business process, logging pipeline events into an audit table, or deleting stale or temporary data. It is usually placed downstream of nodes that determine what write operation to perform (for example, a rules node that decides which rows to update or a template node that builds the final SQL). Upstream, you must provide a valid `credentials_path` pointing to a PostgreSQL credential set and the `sql_text` you want to run. Downstream, use the text result for logs or operator review, and parse the JSON result to inspect fields like status, errors, or affected row count for conditional logic. Combine this node with "SaltPostgresQuery" to verify the effects of a write (e.g., run Execute, then Query to check updated rows), and with schema helper nodes such as "SaltPostgresTableInfo" or "SaltPostgresListTables" to safely construct valid statements. For best results, keep SQL statements focused on a single clear operation, parameterize values in upstream logic where possible, and avoid directly embedding untrusted user input.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reference path or key used by the PostgreSQL integration to load connection credentials. This must point to a configured credential bundle for the `postgres` service, containing host, port, database name, username, and password. If the path is invalid or the credentials are missing, the node will fail before executing any SQL.</td><td style="word-wrap: break-word;">services/postgres/prod_reporting_db</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of seconds to wait for the PostgreSQL execute request to complete. If the operation exceeds this time (for example on large batch updates), the call will time out. Choose a value that balances responsiveness and the expected complexity of your statements.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL statement to execute against PostgreSQL for data modification. This is intended for non-SELECT commands such as INSERT, UPDATE, DELETE, TRUNCATE, and similar. It supports multiline input and must be valid PostgreSQL syntax. Do not include queries intended to return large result sets; use a dedicated query node for that.</td><td style="word-wrap: break-word;">INSERT INTO audit_log (user_id, action, created_at) VALUES (12345, 'password_reset', NOW());</td></tr>
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
<tr><td style="word-wrap: break-word;">text_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable description of the execution result, formatted by the underlying database base node. It is suitable for logs, monitoring, or quick operator review, and typically indicates success or failure and may mention affected row counts.</td><td style="word-wrap: break-word;">PostgreSQL Execute Results =========================== Status: success Operation: INSERT Rows affected: 1</td></tr>
<tr><td style="word-wrap: break-word;">json_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded string containing the raw result from the "/execute" call, including fields such as status, rows_affected, and possibly error information. Downstream nodes can parse this string to implement conditional logic or metrics collection.</td><td style="word-wrap: break-word;">{"status": "success", "operation": "INSERT", "rows_affected": 1, "duration_ms": 18}</td></tr>
<tr><td style="word-wrap: break-word;">html_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML-formatted representation of the result, if produced by the base formatting logic. Often this will be a minimal HTML summary that can be embedded in dashboards or reports. In many simple pipelines, this may be an empty string.</td><td style="word-wrap: break-word;"><h3>PostgreSQL Execute Results</h3><p>Status: success<br/>Operation: INSERT<br/>Rows affected: 1</p></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_output</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary XLSX data representing the execution result as a spreadsheet, when supported by the underlying implementation. For most execute operations this will remain empty or a very small one-row summary file and is optional for downstream consumers.</td><td style="word-wrap: break-word;">Not specified</td></tr>
<tr><td style="word-wrap: break-word;">pdf_output</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF data representing a formatted report of the execution result, when enabled in the base node. This is typically unused for straightforward write operations and often returned as empty.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Large or complex UPDATE/DELETE statements can lock many rows and run for a long time. Use appropriate WHERE clauses, indexes, and batch your writes into smaller chunks where possible, and tune the `timeout` value accordingly.
- **Limitations**: This node is not intended for SELECT queries that return data. Use the PostgreSQL Query or Visual Query nodes for reading rows; using SELECT here may lead to unhelpful or truncated responses.
- **Behavior**: The SQL text is forwarded as-is to the backend "/execute" endpoint. The node does not perform SQL validation, escaping, or parameterization by itself, so upstream logic must avoid directly concatenating untrusted user input into `sql_text`.
- **Behavior**: Connection details and authentication are completely driven by the `credentials_path` and the `postgres` service configuration. If that configuration changes (for example, password rotation or host migration), the node behavior will reflect those changes without modification to the node itself.
- **Limitations**: Transaction scope (auto-commit vs. explicit transactions) is determined by the backend PostgreSQL service configuration and not by this node. Multiple separate node executions are not automatically wrapped in a single database transaction.

## Troubleshooting
- **Common Error 1**: Error indicating credentials could not be loaded or service is unreachable. Check that `credentials_path` is correct, the underlying PostgreSQL service is running, network access is allowed, and that the credential template `postgres` is configured properly.
- **Common Error 2**: PostgreSQL errors such as "syntax error at or near ..." or "relation \"table_name\" does not exist". Review the `sql_text` for typos, correct quoting, proper schema qualification (e.g., `public.users`), and ensure the target tables and columns actually exist.
- **Common Error 3**: Authorization issues like "permission denied for table ..." or "must be owner of relation ...". Confirm that the database user in the credentials has the required INSERT/UPDATE/DELETE privileges, or adjust database grants accordingly.
- **Common Error 4**: Timeouts on heavy operations. Increase the `timeout` parameter, simplify or optimize the SQL (for example by adding indexes or narrowing WHERE clauses), or split the operation into multiple smaller execute calls.
