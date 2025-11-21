# Oracle Test Connection

Validates connectivity to an Oracle database using the provided credentials. The node loads the saved Oracle credentials, attempts a lightweight connection test, and returns a human-readable status along with a JSON result. It does not execute any SQL; it strictly verifies access and responsiveness.

## Usage

Use this node early in a workflow to confirm that your Oracle credentials and network access are configured correctly before running queries or other database operations. Typical usage is to supply a saved credentials file and a reasonable timeout, then route the text/JSON outputs to logs or checks.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">CREDENTIALS</td><td style="word-wrap: break-word;">Path or reference to stored Oracle database credentials that match the Oracle credential template (host/port/service name or SID, username, password, etc.).</td><td style="word-wrap: break-word;">/workspace/credentials/oracle.json</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">TEXT</td><td style="word-wrap: break-word;">Human-readable summary of the connection test result.</td><td style="word-wrap: break-word;">Oracle Connection Test: Success</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured result of the connection test, including status and any diagnostic details.</td><td style="word-wrap: break-word;">{"status": "success", "message": "Connected successfully"}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML-formatted representation of the result (may be empty for this operation).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Spreadsheet output for the result (not typically used for this operation; may be empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF-formatted representation of the result (not typically used for this operation; may be empty).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: Ensure the saved credentials are complete and correct for Oracle (host, port, service name or SID, username, password).
- **No data operations**: This node does not run queries or modify data; it only tests connectivity.
- **Network accessibility**: The target Oracle host/port must be reachable from the environment running the node, and any firewalls/VPC rules must allow traffic.
- **Timeout sensitivity**: Large network latencies or slow endpoints may require increasing the timeout value.
- **Service availability**: The underlying database service must be running and accepting connections for the test to succeed.

## Troubleshooting
- **Connection timed out**: Increase the timeout value and verify network paths, DNS resolution, firewall rules, and that the Oracle listener is reachable on the specified port.
- **Authentication failed**: Confirm username/password in the credentials file and ensure the account is unlocked and has permission to connect.
- **Service name/SID mismatch**: Verify whether you should use a service name or SID and that it matches the database configuration.
- **Invalid credentials file**: Ensure the credentials_path points to a valid file/reference that follows the Oracle credential template and is accessible to the workflow.
- **TLS/SSL issues**: If secure connection is required, confirm any necessary certificates or wallet configurations are correctly set in your environment.
