# MySQL Test Connection

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Validates that Salt can connect to a MySQL database using the provided credentials. It performs a lightweight connectivity check and returns a human-readable status along with the raw JSON response. No data is modified during this operation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqltestconnection.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node early in a workflow to verify that your MySQL credentials and network access are correctly configured before running queries or write operations. Typically connected after a credentials loader or when changing environments (dev/staging/prod).

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the stored MySQL credentials that include host, port, database, username, and password.</td><td style="word-wrap: break-word;"><path-to-mysql-credentials.json></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the connection test before failing.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the connection test result.</td><td style="word-wrap: break-word;">MySQL Connection Test: Success</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string with the raw response from the connection test.</td><td style="word-wrap: break-word;">{"status":"ok","details":{"latency_ms":12}}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation (empty for this node unless provided by underlying formatting).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">XLSX export data placeholder (not used by this node; returns empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">PDF export data placeholder (not used by this node; returns empty).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node requires valid MySQL credentials saved in a credentials file or manager referenced by credentials_path.
- The operation is read-only and intended solely for connectivity validation.
- If SSL or special connection parameters are needed, ensure they are reflected in your credentials or connection string.
- Output includes both a readable message and a JSON string; downstream nodes can parse the JSON for programmatic checks.

## Troubleshooting
- Authentication failed: Verify username/password and that the credentials match the target database.
- Host unreachable or refused: Check host, port, firewall rules, VPN/proxy settings, and that the MySQL server is running.
- Timeouts: Increase the timeout value or ensure network latency is acceptable.
- SSL-related errors: Align client SSL settings with the server configuration and ensure certificates are properly configured.
- Access denied to database: Confirm the user has CONNECT privileges to the specified database.
