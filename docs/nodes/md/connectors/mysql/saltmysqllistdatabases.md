# MySQL List Databases

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lists all databases accessible to the provided MySQL connection. It loads the specified credentials, calls the MySQL service to retrieve database names, and returns both a human-readable summary and a JSON payload.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqllistdatabases.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover available databases on a MySQL server before selecting one for queries or schema exploration. Typically placed early in a workflow to guide dynamic selection of target databases for subsequent operations.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored MySQL credentials to authenticate the request. The credentials should match the 'mysql' template.</td><td style="word-wrap: break-word;">/workspace/credentials/mysql.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation to complete before failing.</td><td style="word-wrap: break-word;">60</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary listing the discovered databases.</td><td style="word-wrap: break-word;">MySQL Databases:\n- information_schema\n- app_db\n- analytics</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the raw result of the database list operation.</td><td style="word-wrap: break-word;">{"data": [{"database": "information_schema"}, {"database": "app_db"}, {"database": "analytics"}]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of results (not used by this node's default output; returned as an empty string).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary XLSX content (not used by this node's default output; returned as an empty value).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF content (not used by this node's default output; returned as an empty value).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials Required**: This node uses the 'mysql' credential template. Ensure the provided credentials path contains valid MySQL connection details.
- **No Additional Parameters**: Beyond credentials and timeout, no extra inputs are required to list databases.
- **Output Structure**: The node returns both a human-readable text summary and a JSON string; other output slots (HTML, XLSX, PDF) are intentionally left empty by default.
- **Permissions Matter**: The databases returned depend on the user's privileges; limited users may see only a subset.

## Troubleshooting
- **Authentication failure**: Verify the credentials file at the provided path and ensure it matches the expected 'mysql' format.
- **Timeouts or network errors**: Increase the timeout value and ensure network connectivity to the MySQL host.
- **Empty results**: Check user permissions; the account may not have privileges to view available databases.
- **Unexpected JSON format**: Ensure downstream nodes parse the 'json' output as a string and decode it before use.
