# Oracle Test Connection

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Validates connectivity to an Oracle database using provided credentials. It sends a lightweight test request to confirm that the host, port, service/SID, and authentication are correct, returning a human-readable status and a structured JSON result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracletestconnection.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node early in a workflow to confirm that your Oracle database credentials and network access are correctly configured before running queries or schema operations. Point it to an Oracle credential file or connection configuration and set a suitable timeout to catch network or authentication issues quickly.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">CREDENTIALS</td><td style="word-wrap: break-word;">Path or reference to the stored Oracle credentials. Must include host, port, service/SID, username, and password (and any required options).</td><td style="word-wrap: break-word;"><path-to-oracle-credentials.json></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time, in seconds, to wait for the test connection to complete before failing.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A concise, human-readable summary of the connection test outcome.</td><td style="word-wrap: break-word;">Oracle Connection Test: Success</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured details about the connection test, including status and any diagnostic messages.</td><td style="word-wrap: break-word;">{"status":"success","message":"Connection established","latency_ms":124}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML-formatted output (may be empty for this operation).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary Excel content for exports (not typically used for connection tests).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF content for exports (not typically used for connection tests).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Ensure the credentials reference uses the Oracle template and contains valid host, port, service name or SID, username, and password.
- The timeout applies to the remote connectivity check; slow networks or database servers may require a higher value.
- This node does not execute SQL; it only validates that a connection can be established.
- Network/firewall rules, SSL/wallet requirements, or VPN constraints can cause failures even with correct credentials.

## Troubleshooting
- Connection failed: Verify host, port, and service/SID in the credentials, and confirm the database is reachable from your network.
- Authentication error: Check username/password in the credentials and any account lock or permission issues on the database.
- Timeouts: Increase the timeout input and re-run; also verify latency, firewall rules, and that the Oracle listener is running.
- SSL/TLS or wallet issues: If your Oracle environment requires wallets or certificates, confirm those settings are correctly configured in the credentials.
- Unexpected errors: Recreate the credentials using the Oracle Connection String node or re-upload a clean credentials file, then test again.
