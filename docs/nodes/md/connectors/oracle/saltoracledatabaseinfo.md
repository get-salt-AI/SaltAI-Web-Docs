# Oracle Database Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves high-level instance information from an Oracle database using the configured credentials. It calls the Oracle service to return details such as version, instance status, and other metadata. Results are returned in a human-readable text summary and structured JSON, with additional export formats supported by the platform.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracledatabaseinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to quickly verify the Oracle instance details during environment setup, health checks, or pre-flight steps before running queries. Typically placed early in a workflow to confirm connectivity and to capture database metadata for logging or auditing.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored Oracle credentials file that follows the 'oracle' credential template (includes host, port, service/SID, username, and password or secret reference).</td><td style="word-wrap: break-word;">/workspace/credentials/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the Oracle service to respond before failing the request.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the Oracle database information.</td><td style="word-wrap: break-word;">Oracle Database Info: Instance 'ORCL' is OPEN, version 19.18.0.0.0.</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured JSON containing the database info returned by the Oracle service.</td><td style="word-wrap: break-word;">{"instance_name": "ORCL", "status": "OPEN", "version": "19.18.0.0.0"}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML-formatted table or content for display or reporting (when available).</td><td style="word-wrap: break-word;"><table><tr><th>instance_name</th><td>ORCL</td></tr><tr><th>status</th><td>OPEN</td></tr></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Excel workbook export of the data (when export is enabled).</td><td style="word-wrap: break-word;"><xlsx-binary-or-file-path></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF export of the data (when export is enabled).</td><td style="word-wrap: break-word;"><pdf-binary-or-file-path></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Credentials must use the 'oracle' credential template and have sufficient privileges to view instance information.
- Network access to the Oracle host must be allowed; ensure firewall rules and security groups permit connectivity.
- Timeout controls the service call duration only; very slow database responses may still require an increased timeout.
- This node relies on the Salt CData service for execution. Ensure the service is deployed and reachable from your environment.
- Returns a text summary and JSON by default. Additional formats (HTML/XLSX/PDF) may be present depending on platform configuration.

## Troubleshooting
- Connection failed: Verify host, port, and service/SID in the credentials file and confirm the Salt CData service is reachable.
- Authentication error: Ensure the username/password or secret reference in the credentials is valid and has required privileges.
- Timeouts: Increase the timeout input value if the Oracle environment is slow or under heavy load.
- Empty or partial data: Confirm the connected user has permissions to access instance metadata views.
- TLS/SSL issues: If using secure connections, verify certificates and connection parameters in the credentials.
