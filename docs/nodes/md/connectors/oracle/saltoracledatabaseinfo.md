# Oracle Database Info

Retrieves high-level information about a connected Oracle database instance using the provided credentials. Returns a human-readable summary and a structured JSON payload with key details like version, instance name, and status.

## Usage

Use this node when you need to verify and capture metadata about your Oracle database environment (e.g., during setup validation, audits, or health checks). Typically placed after a credentials/configuration step and before any query or schema exploration nodes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">CREDENTIALS</td><td style="word-wrap: break-word;">Path or reference to stored Oracle database credentials that follow the Oracle credential template.</td><td style="word-wrap: break-word;">/configs/credentials/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time to wait for the database info request to complete. Increase if your database has high latency or slow responses.</td><td style="word-wrap: break-word;">120000</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A readable summary of the Oracle database instance information.</td><td style="word-wrap: break-word;">Oracle Database Info: Version 19c, Instance ORCL, Status OPEN</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Structured JSON string with detailed database info (e.g., version, instance name, status, platform).</td><td style="word-wrap: break-word;">{"version":"19.18.0.0.0","instance":"ORCL","status":"OPEN","platform":"Linux x86-64"}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of the results (may be empty when using default output formatting).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary content for an Excel export (empty when using default output formatting).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary content for a PDF export (empty when using default output formatting).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials**: Requires valid Oracle credentials that match the Oracle credential template.
- **Timeouts**: Long-running or slow connections may require increasing the timeout to avoid failures.
- **Permissions**: The connected user must have sufficient privileges to read database instance metadata.
- **No extra parameters**: This node does not require schema or table inputs; it reports instance-level details.

## Troubleshooting
- **Authentication failed**: Ensure the credentials file/reference is correct and follows the Oracle template. Verify username/password and network access.
- **Connection timeout**: Increase the timeout value and confirm that the database host and port are reachable from the environment where this node runs.
- **Insufficient privileges**: Use a user with rights to query instance metadata or consult your DBA to grant the necessary permissions.
- **Empty or partial results**: Check database availability and user permissions; also verify that the instance is open and accessible.
