# Oracle Test Connection

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Validates connectivity to an Oracle database using the provided credentials. It performs a lightweight test and returns both a human-readable status summary and a structured JSON response with details.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracletestconnection.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node early in your workflow to verify that your Oracle credentials, host, port, and database settings are correct before running queries or metadata operations. Typical usage is immediately after loading or selecting Oracle credentials to ensure the environment is reachable and authentication works.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the saved Oracle credentials file that follows the Oracle credential template.</td><td style="word-wrap: break-word;">/workspace/credentials/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the connection test to complete.</td><td style="word-wrap: break-word;">60</td></tr>
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
<tr><td style="word-wrap: break-word;">string</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A concise, human-readable summary of the connection test result.</td><td style="word-wrap: break-word;">Oracle Connection Test: Success</td></tr>
<tr><td style="word-wrap: break-word;">json_string</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string with structured details of the test outcome, which may include status, messages, and diagnostic information.</td><td style="word-wrap: break-word;">{"status":"success","details":"Connected to Oracle database","latency_ms":128}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credential Template**: This node uses the Oracle credential template. Ensure your credentials file matches the required schema for Oracle (e.g., host, port, database/service, username, password, and any SSL settings as applicable).
- **No extra parameters**: The node does not require additional inputs beyond credentials and timeout for the connection test.
- **Network access**: The environment running this node must have network access to the Oracle host/port. Firewalls, VPNs, or security groups may block connectivity.
- **Timeout behavior**: If the database is slow to respond or the network is constrained, increase the timeout value accordingly.

## Troubleshooting
- **Authentication failed**: Verify username/password in the credentials file and ensure the account is not locked or expired.
- **Host unreachable or timeout**: Check network connectivity, DNS resolution, firewall rules, and that the Oracle listener is running on the specified host/port.
- **Service name/SID mismatch**: Confirm that the credentials reference the correct Oracle service (SERVICE_NAME) or SID expected by the server.
- **SSL or wallet issues**: If using SSL/TLS or an Oracle wallet, confirm all required files and settings are correctly referenced in the credentials and accessible to the runtime.
