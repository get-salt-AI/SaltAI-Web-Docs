# MySQL Test Connection

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Validates that Salt can connect to a MySQL database using the provided credentials. It performs a lightweight connectivity check and returns a human-readable summary along with a JSON payload describing the result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqltestconnection.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node early in a workflow to verify that your MySQL credentials and network access are correct before running queries or other database operations. Typical usage is to provide a path to saved MySQL credentials and a reasonable timeout, then branch your workflow based on success or failure.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored MySQL credentials file that follows the MySQL credential template (host, port, database, username, password, and optional SSL settings).</td><td style="word-wrap: break-word;">/workspace/credentials/mysql.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the connection test to complete before failing.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the connection test outcome.</td><td style="word-wrap: break-word;">MySQL Connection Test: Success</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON response containing connection test details (e.g., success flag, message, and any diagnostic info).</td><td style="word-wrap: break-word;">{"success": true, "message": "Connection established."}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML output (not used for this node; will be empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">XLSX output (not used for this node; will be empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">PDF output (not used for this node; will be empty).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials**: Ensure your credentials file matches the MySQL template (including host, port, database, username, and password).
- **Network Access**: The environment running this node must be able to reach the MySQL host/port.
- **No Data Changes**: This node does not execute SQL; it only checks connectivity.
- **Timeouts**: Slow networks or strict firewalls may require increasing the timeout value.

## Troubleshooting
- **Authentication failed**: Verify username/password and that the user has permission to access the specified database.
- **Host unreachable or DNS error**: Check the host/IP, port, VPC/firewall rules, and that the MySQL server is running.
- **SSL/Certificate issues**: If your server requires SSL, ensure SSL-related options are correctly set in your credentials.
- **Operation timed out**: Increase the timeout input and re-run; also verify network latency and server load.
- **Access denied to database**: Confirm the database name exists and the provided user has privileges on it.
