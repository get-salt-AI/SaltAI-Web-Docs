# MySQL List Tables

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lists all tables available in a specified MySQL database. It authenticates using provided MySQL credentials and returns a human-readable list along with a machine-readable JSON payload. Non-text exports (HTML/XLSX/PDF) are available but are empty by default for this operation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqllisttables.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover or audit the tables present in a particular MySQL database before writing queries or building data workflows. Typical usage is to connect with valid MySQL credentials, provide the target database name, and feed the results into downstream nodes that select tables, inspect schemas, or construct queries.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the saved MySQL credentials file configured for this service. The credentials should follow the MySQL credential template.</td><td style="word-wrap: break-word;">/workspace/credentials/mysql.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the list operation before failing.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the MySQL database from which to list tables.</td><td style="word-wrap: break-word;">analytics</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">TEXT</td><td style="word-wrap: break-word;">Formatted textual summary of tables found in the specified database.</td><td style="word-wrap: break-word;">Tables in Database: analytics - users - orders - products</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">JSON representation of the result, suitable for programmatic use.</td><td style="word-wrap: break-word;">{"data": ["users", "orders", "products"], "database": "analytics"}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML output (empty by default for this node).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Binary Excel data for export (empty by default for this node).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">Binary PDF data for export (empty by default for this node).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: Ensure the credentials file at credentials_path is valid and configured for MySQL.
- **Database name accuracy**: The database input must exist and be accessible to the provided user, otherwise the result may be empty or error.
- **Timeouts**: Large catalogs or slow connections may require increasing the timeout.
- **Outputs**: The operation primarily returns populated TEXT and JSON outputs; HTML/XLSX/PDF outputs are provided for interface consistency and are typically empty for this node.

## Troubleshooting
- **Authentication error**: Verify credentials_path points to a valid MySQL credential set and that network access to the MySQL host is allowed.
- **Permission denied**: Ensure the MySQL user has privileges to read metadata (e.g., information_schema) for the specified database.
- **Empty results**: Confirm the database name is correct and that it actually contains tables.
- **Request timed out**: Increase the timeout input and check database/network latency.
- **Unknown database**: Double-check the database field for typos or missing databases on the server.
