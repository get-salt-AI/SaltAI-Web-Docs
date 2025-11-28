# MySQL Test Connection

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Validates that Salt can connect to a MySQL database using the provided credentials. It performs a lightweight connectivity check and returns a human-readable status along with the raw JSON response.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqltestconnection.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node right after configuring your MySQL credentials to confirm connectivity before running any queries or schema operations. It’s typically placed at the start of a workflow to fail fast if the database is unreachable or credentials are incorrect.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the saved MySQL credentials that follow the MySQL credential template (e.g., host, port, database, username, password, and optional SSL details).</td><td style="word-wrap: break-word;">/workspace/credentials/mysql.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the connection test before failing.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">TEXT</td><td style="word-wrap: break-word;">Human-readable summary of the connection test (e.g., success or error message).</td><td style="word-wrap: break-word;">MySQL Connection Test: Success</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON payload with detailed result data from the connection test.</td><td style="word-wrap: break-word;">{"status":"ok","message":"Connection successful"}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML representation (not used by this node; returned empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary Excel data (not used by this node; returned empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF data (not used by this node; returned empty).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node uses the MySQL credential template. Ensure your stored credentials include the correct host, port, database, username, and password, and any required SSL settings.
- The operation is read-only and does not modify any data; it only tests connectivity.
- Network access to the MySQL server must be permitted from the Salt runtime environment (firewalls, security groups, VPN, and allowlists must be correctly configured).
- If your MySQL server enforces SSL, ensure the SSL mode and related parameters in your credentials match the server configuration.
- The output includes both a human-readable summary and a JSON object to facilitate both visual checks and programmatic branching in workflows.

## Troubleshooting
- Authentication failed: Verify username/password, database name, and that the user has permission to connect from the Salt runtime’s IP/host.
- Network timeout or unreachable host: Check host/port, firewall rules, VPC/VPN connectivity, and increase the timeout if needed.
- SSL/TLS errors: Align SSL mode and certificates with server settings (e.g., REQUIRED, VERIFY_CA, or VERIFY_IDENTITY).
- Access denied for user or database does not exist: Confirm the database exists and the user privileges are correctly granted for that database.
- Intermittent connection issues: Check MySQL server availability, connection limits, and any load balancers or proxies in the path.
