# Oracle Test Connection

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Validates connectivity to an Oracle database using the saved Oracle credentials. It performs a lightweight health check against the database and returns a human-readable summary along with a structured JSON payload. No SQL is executed; this is strictly a connection test.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracletestconnection.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node right after setting up Oracle credentials to confirm they work before running queries or metadata operations. It’s also useful for diagnosing network, authentication, or configuration issues when a workflow first interacts with an Oracle database.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored Oracle credential set (created from the 'oracle' credential template). Must include connection details such as host, port, service name/SID, username, and password.</td><td style="word-wrap: break-word;">/data/credentials/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the connection test before failing with a timeout.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable connection test result summary (success or failure details).</td><td style="word-wrap: break-word;">Oracle Connection Test: Success</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured result of the connection test, including status and any diagnostic information or error messages.</td><td style="word-wrap: break-word;">{"status":"ok","message":"Connected successfully","latency_ms":124}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Credentials must follow the Oracle credential template, including host, port, and either service name or SID, along with username and password.
- This node only tests connectivity; it does not run any SQL statements or modify data.
- Network policies, firewalls, VPNs, or database listener settings can affect connectivity; adjust the timeout if the environment is slow to respond.
- If SSL/TLS or wallet-based connections are required in your environment, ensure those settings are included in the stored credentials.
- Never embed secrets directly in the workflow; reference a secure credentials file via credentials_path.

## Troubleshooting
- Connection times out: Increase the timeout value and verify network reachability (host, port, VPN, firewall, and Oracle listener status).
- Authentication failed: Check username/password and ensure the correct service name or SID is used; verify the account is not locked and has connect privileges.
- ORA- errors returned: Consult the error code details; verify the database is up, the listener is running, and the provided service name/SID matches the server configuration.
- SSL/TLS or wallet errors: Confirm that certificate/wallet paths and parameters are correctly included in the credentials and accessible to the node runtime.
- Invalid credentials_path: Ensure the file exists, is readable, and matches the 'oracle' credential template structure.
