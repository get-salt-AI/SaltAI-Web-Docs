# MySQL Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes a MySQL SELECT query against a configured MySQL database and returns formatted results. It loads credentials from a provided path, sends the query to the MySQL service, and returns both a human-readable summary and a JSON payload. Designed for read-only queries; use the Execute node for INSERT/UPDATE/DELETE.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to retrieve data from a MySQL database within a workflow. Provide a valid credentials file/path and a SELECT statement. Typical usage is to preview data, feed results into downstream processing, or export results in multiple formats via companion nodes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the stored MySQL credentials configuration that matches the 'mysql' credential template.</td><td style="word-wrap: break-word;">/path/to/credentials/mysql.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the query to complete before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A MySQL SELECT statement to execute. Multiline supported. Use only read-only queries here.</td><td style="word-wrap: break-word;">SELECT id, name, email FROM users ORDER BY created_at DESC LIMIT 10;</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the query results (e.g., row count and formatted rows).</td><td style="word-wrap: break-word;">MySQL Query Results (10 rows): ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the raw query result payload suitable for parsing downstream.</td><td style="word-wrap: break-word;">{"data": [{"id":1,"name":"Alice"}], "row_count": 1}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML table representation of the results (may be empty if not generated).</td><td style="word-wrap: break-word;"><table><thead>...</thead><tbody>...</tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary data for an Excel file containing the results (may be empty if not generated).</td><td style="word-wrap: break-word;"><binary-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary data for a PDF rendering of the results (may be empty if not generated).</td><td style="word-wrap: break-word;"><binary-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Only SELECT/read-only queries should be executed with this node. For INSERT/UPDATE/DELETE, use the 'MySQL Execute' node.
- Credentials must conform to the 'mysql' credential template. Ensure host, port, database, username, and password are valid.
- Queries are subject to timeouts; large or complex queries may need higher timeout settings or additional filtering/limits.
- Returned formats beyond text/JSON may be empty unless a flow or companion node specifically requests/produces them.
- Avoid constructing SQL from untrusted input to prevent SQL injection risks.

## Troubleshooting
- Connection/authentication failed: Verify credentials_path points to a valid MySQL configuration with correct host, port, database, username, and password.
- Query timed out: Increase the timeout input or simplify/add LIMIT to your query.
- SQL syntax error: Validate your SELECT statement syntax and table/column names in the target database.
- Empty results: Confirm the query conditions match existing data; test with a simpler query or remove overly restrictive filters.
- Unexpected output format is empty: This node primarily guarantees text and JSON. If you need HTML/XLSX/PDF, ensure downstream steps or format-specific nodes generate those formats.
