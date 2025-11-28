# MySQL List Databases

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lists all databases available to the provided MySQL connection. It loads saved MySQL credentials, calls the service to retrieve database names, and returns both a readable summary and machine-friendly JSON.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqllistdatabases.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to discover which databases are accessible with your MySQL credentials before selecting one for queries, table listing, or schema exploration. It’s typically placed early in a workflow to drive subsequent selections for table info, queries, or visual query building.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored MySQL credentials. Must correspond to a MySQL credential template.</td><td style="word-wrap: break-word;">/configs/credentials/mysql/default.json</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the databases found.</td><td style="word-wrap: break-word;">MySQL Databases: - information_schema - sales - analytics - mysql</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON payload containing the databases list and any related metadata from the service.</td><td style="word-wrap: break-word;">{"data": ["information_schema", "sales", "analytics", "mysql"], "count": 4}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML output (empty for this node).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Excel output (empty for this node).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">PDF output (empty for this node).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials**: Requires valid MySQL credentials with sufficient permissions to list databases (e.g., SHOW DATABASES).
- **No extra parameters**: This operation does not require additional inputs beyond credentials_path and timeout.
- **Service-backed**: The node delegates work to the MySQL service endpoint; network connectivity and service availability are required.
- **Output formats**: This node primarily returns text and JSON; other formats (HTML/XLSX/PDF) are empty for this operation.

## Troubleshooting
- **Connection failed or timeout**: Verify the host, port, and network access. Increase the timeout value if the database is slow to respond. Use the 'MySQL Test Connection' node to confirm connectivity.
- **Authentication error**: Ensure the credentials_path points to a valid MySQL credential set and that username/password are correct.
- **Insufficient privileges**: The account must have permission to list databases. Contact your DBA or update the account permissions.
- **Empty or partial results**: Confirm the user’s access scope; some environments restrict visibility to specific databases.
