# PostgreSQL Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a read-only SQL SELECT query against a PostgreSQL database using your configured Salt credentials. It loads the specified credential set, sends the query to the PostgreSQL service, and returns a human-readable summary plus the raw JSON payload. This node is intended for querying only and does not modify data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to fetch data from a PostgreSQL database. Provide a valid credentials file path and a SELECT statement. The node returns a formatted text preview and a JSON string of the results for downstream processing. For INSERT/UPDATE/DELETE, use the PostgreSQL Execute node instead.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the saved PostgreSQL credentials file that follows the 'postgres' credential template.</td><td style="word-wrap: break-word;">/secure/creds/postgres.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the database operation before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL SELECT statement to execute. Multiline is supported. Use this node for read-only queries.</td><td style="word-wrap: break-word;">SELECT id, name, email FROM users ORDER BY id DESC LIMIT 10</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable formatted summary of the query results.</td><td style="word-wrap: break-word;">PostgreSQL Query Results (10 rows): ================================================== Row 1:   id: 1   name: Jane Doe   email: jane@example.com ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the raw query result payload (including data and optional metadata such as row counts).</td><td style="word-wrap: break-word;">{"data":[{"id":1,"name":"Jane Doe","email":"jane@example.com"}],"row_count":1}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML representation of results (empty for this node's default output).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary Excel data for results (empty for this node's default output).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF data for results (empty for this node's default output).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Read-only**: This node is for SELECT queries only. Use the PostgreSQL Execute node for INSERT/UPDATE/DELETE.
- **Credentials required**: The provided credentials must follow the 'postgres' template and have sufficient read permissions.
- **Timeouts**: Long-running queries may exceed the timeout. Increase the timeout input or optimize the query.
- **Result size**: Very large result sets can be slow to transfer and render. Use LIMIT/OFFSET and filtering to manage size.
- **Security**: Avoid constructing queries from untrusted input. Parameter binding is not supported in this node's interface.
- **Output formats**: Only text and JSON are populated by default. If you need HTML, Excel, or PDF, consider the PostgreSQL Visual Query Builder node.

## Troubleshooting
- **Authentication failed**: Verify credentials_path points to a valid file and that the credentials are correct and current.
- **Invalid SQL syntax**: Ensure sql_text is a valid PostgreSQL SELECT statement. Test it directly against the database if needed.
- **Timeout errors**: Increase the timeout input or add LIMIT/filters to your query to reduce runtime.
- **Empty or unexpected results**: Check schema/table names, WHERE conditions, and user permissions. Try a simpler SELECT to validate connectivity.
- **Permission denied**: Ensure the database user in the credentials has read access to the target schema and tables.
- **Service/network issues**: If you see connection or network errors, verify the database is reachable from the environment and that the Salt PostgreSQL service is healthy.
