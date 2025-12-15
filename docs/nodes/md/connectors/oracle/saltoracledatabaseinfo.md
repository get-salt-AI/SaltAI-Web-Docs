# Oracle Database Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves instance-level information from an Oracle database using the provided credentials. Returns a human-readable summary along with the raw JSON payload. This node does not require any additional Oracle-specific parameters.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracledatabaseinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to quickly verify and inspect Oracle database instance metadata (such as version, host, and status) after setting up credentials. It’s typically placed early in a workflow to validate connectivity and environment details before running queries or schema introspection.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the saved Oracle credentials that include connection details (host, port, service/SID, username, password).</td><td style="word-wrap: break-word;">/configs/credentials/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the request to complete before timing out.</td><td style="word-wrap: break-word;">60</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Readable summary of the Oracle database information.</td><td style="word-wrap: break-word;">Oracle Database Info: Version 19c, Status OPEN, Service XE</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON payload of the database info response for programmatic use.</td><td style="word-wrap: break-word;">{"version":"19c","status":"OPEN","service_name":"XE","host":"db-host","port":1521}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML output (unused for this node’s default formatting; typically empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">XLSX binary output (unused for this node’s default formatting; typically empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF binary output (unused for this node’s default formatting; typically empty).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: Valid Oracle connection details must be provided via credentials_path.
- **Read-only operation**: This node only retrieves metadata and does not modify any data.
- **Timeout behavior**: If the Oracle server is slow or unreachable, increase the timeout value.
- **Output formats**: By default, only text and JSON are populated; HTML/XLSX/PDF are left empty.

## Troubleshooting
- **Connection failed or timed out**: Verify host, port, and network access; increase the timeout value if needed.
- **Authentication error**: Check username/password and that the account has permission to access instance metadata.
- **Service/SID mismatch**: Ensure the credentials correctly specify service name vs. SID according to your Oracle setup.
- **Empty or minimal info**: Confirm the Oracle instance is running and accessible, and that the user has adequate privileges.
