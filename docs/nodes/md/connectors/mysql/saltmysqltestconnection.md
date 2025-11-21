# MySQL Test Connection

Validates that Salt can connect to a MySQL database using the provided credentials. It sends a lightweight test request and returns a human-readable status message along with the raw JSON response. No schema or data operations are performed; this node is purely for connectivity verification.

## Usage

Use this node early in a workflow to confirm that your MySQL credentials, host, port, and network access are correct before running queries or schema operations. It’s typically placed after configuring credentials and before any query/execute nodes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored MySQL credentials matching the 'mysql' credential template. The node loads these to attempt a connection.</td><td style="word-wrap: break-word;">/credentials/mysql/<your-credential-id>.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the connection test before timing out.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON payload returned by the service, useful for programmatic inspection.</td><td style="word-wrap: break-word;">{"status":"ok","details":{"host":"db.example.com","latency_ms":42}}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML output (not used for this test; typically empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Excel data (not used for this test; typically empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">PDF data (not used for this test; typically empty).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node uses the 'mysql' credential template; ensure your saved credentials conform to it.
- Only connectivity is tested; no tables, schemas, or data are accessed.
- If the service is unreachable or credentials are invalid, the text output will summarize the failure and the json output will include error details.
- Timeout applies to the connectivity check; very large timeouts can delay failure detection.

## Troubleshooting
- Authentication failed: Verify username, password, and that the credentials were saved under the 'mysql' template.
- Host unreachable or port blocked: Confirm network access, correct host/port, and that the database allows inbound connections from the Salt environment.
- SSL/TLS errors: Adjust SSL settings in your stored credentials (e.g., SSL mode) to match the server configuration.
- Operation timed out: Increase the timeout input or check for network latency/firewalls.
- Intermittent failures: Check database server load, connection limits, or transient network issues and retry.
