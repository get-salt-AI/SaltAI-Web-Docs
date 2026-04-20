# MySQL Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node runs a MySQL SELECT statement against a configured MySQL database using credentials managed in Salt. It calls a MySQL service endpoint via a shared database base node and returns both human-readable text and structured JSON results for further processing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to read data from MySQL with a custom SELECT query. Place it after whichever component or configuration provides a valid credentials_path (the MySQL connection profile), and before nodes that analyze, summarize, or export tabular data (for example, reporting or AI analysis nodes). You provide the SQL in sql_text and the node handles authentication, connection, and execution through the MySQL service. It pairs well with helper nodes such as SaltMySQLListTables and SaltMySQLTableInfo for schema exploration, and with SaltMySQLVisualQuery or SaltMySQLQueryBuilder to help design queries before executing the final SQL here. Use this node only for read operations; for inserts, updates, or deletes use SaltMySQLExecute instead.

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
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL SELECT statement to execute. Must be valid MySQL syntax and should only perform read operations (SELECT with optional WHERE, JOIN, GROUP BY, ORDER BY, LIMIT, etc.). Multiline queries are allowed; avoid multiple statements in one call and ensure proper quoting of identifiers and literals.</td><td style="word-wrap: break-word;">SELECT id, email, created_at FROM users WHERE status = 'active' AND created_at >= '2024-01-01' ORDER BY created_at DESC LIMIT 100</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable rendering of the query results, formatted for easy inspection. Useful for logs, chat-style responses, or quick validation of what the query returned.</td><td style="word-wrap: break-word;">MySQL Query Results (2 rows): ================================================== Row 1:   id: 101   email: alice@example.com   created_at: 2024-01-05 09:12:33  Row 2:   id: 102   email: bob@example.com   created_at: 2024-01-06 14:47:10</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded representation of the query results as returned by the MySQL service and formatted by the base node. Typically contains a "data" array of row objects and may include metadata like "row_count". This is the primary output for downstream programmatic use.</td><td style="word-wrap: break-word;">{"data": [{"id": 101, "email": "alice@example.com", "created_at": "2024-01-05T09:12:33"}, {"id": 102, "email": "bob@example.com", "created_at": "2024-01-06T14:47:10"}], "row_count": 2}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML rendering of the result set when provided by the base node’s formatter. Suitable for dashboards, embedded web views, or rich report outputs. In some configurations this may be an empty string.</td><td style="word-wrap: break-word;"><h3>MySQL Query Results</h3><table><thead><tr><th>id</th><th>email</th><th>created_at</th></tr></thead><tbody><tr><td>101</td><td>alice@example.com</td><td>2024-01-05 09:12:33</td></tr><tr><td>102</td><td>bob@example.com</td><td>2024-01-06 14:47:10</td></tr></tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Excel (XLSX) binary content when the base node is configured to produce spreadsheet exports for query results. Often empty for simple setups. Use this to provide downloadable Excel files in downstream steps.</td><td style="word-wrap: break-word;"><binary xlsx data representing a worksheet containing the query result rows></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">PDF binary content when the base node generates a PDF report version of the result set. Often empty unless explicit PDF export is enabled. Useful for static reports or attachments.</td><td style="word-wrap: break-word;"><binary pdf data representing a formatted table of the query results></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Unbounded or complex SELECT queries (for example, SELECT * on large tables without WHERE/LIMIT, or heavy joins) can be slow and may hit the timeout. Add filters, limits, and indexes wherever possible.
- **Limitations**: This node is intended for read-only use. Do not send INSERT, UPDATE, DELETE, or DDL statements; for write operations use the SaltMySQLExecute node.
- **Behavior**: Credential loading, network calls, and base formatting are handled by a shared DatabaseBaseNode. Connection details (host, SSL, ports) and low-level error messages come from that shared layer, not this node itself.
- **Behavior**: The json output is a string containing JSON data, not a parsed object. Downstream nodes must parse it to access individual rows or fields.
- **Security**: Avoid building sql_text by naive string concatenation with untrusted user input. Use safe templating or validation in upstream nodes to reduce SQL injection risk.

## Troubleshooting
- **Common Error 1**: Error text plus a JSON payload like {"error": "..."} indicates the MySQL service rejected the query (syntax error, permission issue, or timeout). Validate the SQL in a MySQL client, simplify it, or adjust permissions and timeout settings.
- **Common Error 2**: Empty data in the json output (but no error) means the query succeeded but found no rows. Recheck WHERE conditions, date ranges, and that the credentials are pointing to the intended database/schema.
- **Common Error 3**: Messages about connection failures or authentication problems usually mean the credentials_path is misconfigured or the database is unreachable. Verify host, port, username, password, and database, and optionally test connectivity with SaltMySQLTestConnection.
- **Common Error 4**: If downstream nodes struggle with large payloads, the result set may be too big. Narrow your SELECT columns, add LIMIT and OFFSET for pagination, or pre-aggregate data in SQL before passing it on.
