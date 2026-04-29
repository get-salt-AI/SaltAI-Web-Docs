# MySQL Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

MySQL Execute runs data-changing SQL statements such as INSERT, UPDATE, DELETE, and DDL (CREATE, ALTER, DROP) against a MySQL database using stored credentials. It sends your SQL to the MySQL service execute endpoint, waits for completion, and then formats the outcome into a human-readable text summary and a JSON payload. This lets you inspect what happened and drive downstream logic from the structured result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever your workflow needs to modify MySQL data or schema instead of reading it. Typical scenarios include inserting new records after processing inputs, updating order or ticket statuses, deleting obsolete data, or applying schema changes within a controlled automation pipeline.

In a typical pipeline, you would use MySQL Test Connection early to validate connectivity, then possibly MySQL List Databases and MySQL List Tables to explore what you can modify. Nodes like MySQL Table Info, MySQL Query Builder Helper, or MySQL Visual Query Builder can help you design the correct table structure and statements. Once you have the final non-SELECT SQL, pass it into MySQL Execute along with the proper credentials. Downstream, you can parse the JSON output to check fields such as success, affected_rows, or insert_id and branch your workflow accordingly or trigger follow-up reads via MySQL Query.

This node works well together with MySQL Connection String (for building connection URIs used in your credential configuration) and with logic or control-flow nodes that implement retries or conditional execution based on the JSON result. Use it in production only after testing statements against a staging environment, and keep SQL generation controlled to avoid unsafe dynamic queries.

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
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL statement to execute against MySQL. Designed for INSERT, UPDATE, DELETE, and DDL operations, not for SELECT queries. Multiline is supported. The SQL must be valid for the target MySQL version and safe to run under the configured user privileges. If you interpolate user input, ensure it is sanitized or parameterized upstream.</td><td style="word-wrap: break-word;">INSERT INTO customers (email, first_name, last_name, created_at) VALUES ('alice@example.com', 'Alice', 'Nguyen', NOW());</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary string describing the outcome of the execute operation. Commonly includes whether the statement was successful, how many rows were affected, and may mention insert IDs or error information. Intended for logs, operator review, or display in user interfaces.</td><td style="word-wrap: break-word;">MySQL Execute Results: Statement executed successfully. Rows affected: 1 Last insert ID: 10427.</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded string with the raw result returned by the MySQL backend service. Typically contains fields such as success (boolean), affected_rows (integer), insert_id (integer or null), warnings (list), and error (object or string) when something goes wrong. Downstream nodes should parse this JSON to implement conditional behavior, error handling, or to capture generated keys.</td><td style="word-wrap: break-word;">{"success": true, "affected_rows": 1, "insert_id": 10427, "warnings": []}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Large batch updates, deletes without selective WHERE clauses, or heavy DDL (for example ALTER TABLE on big tables) can be slow and lock significant portions of the database; keep statements targeted and schedule heavy operations during low-traffic windows.
- **Limitations**: This node does not return row sets from SELECT queries; using SELECT here may result in backend errors or empty data. Use the MySQL Query or MySQL Visual Query Builder nodes when you need to retrieve and inspect table data.
- **Behavior**: Execution is delegated to a remote MySQL service via an execute endpoint; the exact shape of the JSON result may vary slightly by environment or connector version, so always inspect a sample output before building strict parsers around it.
- **Behavior**: The node does not wrap multiple statements in explicit transactions on its own. If you need atomic multi-step updates, manage transactions directly in your SQL (using BEGIN, COMMIT, and ROLLBACK) or through database-side configuration.

## Troubleshooting
- **Common Error 1**: The text output includes an error message such as "syntax error near" and the JSON output has success set to false. Verify that sql_text is valid MySQL syntax, ensure table and column names are correct, and test the statement directly on the target database.
- **Common Error 2**: Execution times out, especially on large tables or complex DDL. Increase the timeout input, optimize the query with indexes and selective WHERE clauses, or break the operation into smaller batches. Confirm there are no blocking locks on the affected tables.
- **Common Error 3**: JSON output indicates access denied or insufficient privileges. Update the credentials referenced by credentials_path to grant necessary INSERT, UPDATE, DELETE, or DDL privileges, or change the target database or schema to one where the user has appropriate rights.
