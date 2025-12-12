# MySQL Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a SQL SELECT query against a MySQL database using configured credentials. It returns a readable text summary of the results and the raw JSON payload. This node is read-only and is intended for data retrieval; use a different node for INSERT/UPDATE/DELETE.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to fetch data from MySQL as part of a workflow. Provide a valid credentials reference and a SELECT statement. Typical flow: validate connection with MySQL Test Connection, optionally explore schema with List Databases/Tables or Table Info, then run your query here and pass its outputs to downstream nodes that format, analyze, or export the data.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reference to saved MySQL credentials (for example, a credentials file or managed secret path) that includes host, port, database, username, and password or a connection string.</td><td style="word-wrap: break-word;"><path-or-secret-ref-to-mysql-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the query to complete before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL SELECT query to execute. Use standard MySQL syntax; consider adding LIMIT to control result size.</td><td style="word-wrap: break-word;">SELECT id, name, email FROM users ORDER BY created_at DESC LIMIT 50</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the query results, including row counts and formatted rows.</td><td style="word-wrap: break-word;">MySQL Query Results (50 rows): ================================================== Row 1:   id: 123   name: Jane Doe   email: jane@example.com ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON result of the query for programmatic use.</td><td style="word-wrap: break-word;">[{"id":123,"name":"Jane Doe","email":"jane@example.com"},{"id":124,"name":"John Smith","email":"john@example.com"}]</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML table representation of the results. This node returns an empty string for this field.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary Excel data for the results. This node returns an empty value for this field.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF data for the results. This node returns an empty value for this field.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Read-only**: Intended for SELECT queries only. Use the MySQL Execute node for INSERT/UPDATE/DELETE.
- **Credentials required**: Ensure the credentials reference is valid and points to a MySQL instance accessible from the runtime environment.
- **Performance**: For large tables, include LIMIT and WHERE clauses to avoid timeouts or excessive payload sizes.
- **Security**: Avoid concatenating untrusted input directly into SQL to prevent SQL injection.
- **Character sets**: If you encounter encoding issues, verify the connection's charset configuration in your credentials or connection string.
- **Output behavior**: This node returns text and JSON. HTML/XLSX/PDF outputs are provided as empty placeholders.

## Troubleshooting
- **Authentication failed**: Verify host, port, database, username, password, and SSL settings in the credentials. Test with the MySQL Test Connection node.
- **Query timeout**: Increase the timeout input, add LIMIT, or optimize the query with indexes and narrower WHERE clauses.
- **Syntax error**: Validate the SQL in a MySQL client. Ensure the statement is a valid SELECT and references the correct database and tables.
- **Empty results**: Confirm the database and table names, schemas, and WHERE conditions. Try removing filters to test connectivity.
- **Encoding/emoji issues**: Set or adjust the charset (e.g., utf8mb4) in the credentials or connection string.
- **Permission denied**: Check the database user's privileges for SELECT on the targeted tables.
