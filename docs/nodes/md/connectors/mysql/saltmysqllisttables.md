# MySQL List Tables

Lists all tables available in a specified MySQL database using configured credentials. Returns a human-readable summary and a JSON payload, with optional HTML, XLSX, and PDF formatted outputs for downstream reporting or visualization.

## Usage

Use this node when you need to explore schema structure or drive UI pickers by enumerating tables in a MySQL database. Typical workflows include running it before table info or query nodes to help users select a table, or exporting the table list for documentation and audits.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored MySQL credentials that include connection details and authentication.</td><td style="word-wrap: break-word;">/connections/mysql/prod.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before timing out.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The MySQL database name to list tables from.</td><td style="word-wrap: break-word;">analytics_db</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Plain-text summary of the tables found in the specified database.</td><td style="word-wrap: break-word;">Tables in Database: analytics_db - users - events - sessions</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the raw result of the list tables operation.</td><td style="word-wrap: break-word;">{"data": [{"table_name": "users"}, {"table_name": "events"}]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation (e.g., a table) of the listed tables for embedding in dashboards or reports. May be empty if not generated.</td><td style="word-wrap: break-word;"><h3>Tables in Database: analytics_db</h3><table>...</table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary XLSX data containing the table list for download or archival. May be empty if not generated.</td><td style="word-wrap: break-word;"><binary-xlsx-data></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF data with the table list formatted for printing/sharing. May be empty if not generated.</td><td style="word-wrap: break-word;"><binary-pdf-data></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: The node uses the MySQL credential template and must have valid host, port, database, and auth to access metadata.
- **Permissions matter**: The connected user must have privileges to read schema metadata; otherwise results may be empty or partial.
- **Database parameter**: Defaults may point to 'mysql', but set the target application database explicitly to get relevant tables.
- **Timeouts**: Large schemas or slow networks may require increasing the timeout to avoid premature failures.
- **Output formats**: The node always returns text and JSON; HTML/XLSX/PDF outputs may be empty unless specifically produced by the underlying formatter.

## Troubleshooting
- **Authentication error**: Verify the credentials_path points to valid MySQL credentials and that the username/password are correct.
- **Unknown database**: Ensure the 'database' value exists on the target server and matches case/quoting rules.
- **Empty results**: Confirm the user has metadata access (e.g., to information_schema) and that the database actually contains tables.
- **Network timeout**: Increase the 'timeout' value or check network/firewall restrictions between the node runtime and the MySQL server.
- **Intermittent failures**: Check server load and connection limits; reduce concurrent requests or retry with backoff.
