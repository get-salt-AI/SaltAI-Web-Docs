# Oracle Database Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves high-level information about a connected Oracle database instance. It loads credentials from a database URI and calls the Oracle data connector to return details as human-readable text and raw JSON. The node returns only text and JSON outputs (no HTML/XLSX/PDF exports).

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracledatabaseinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to quickly inspect the Oracle database instance (e.g., version, current user, instance metadata) as part of a database diagnostics or setup workflow. Provide a valid Oracle connection URI in credentials_path, optionally adjust timeout, and pass its outputs to logging or decision nodes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Oracle database URI used to authenticate and route the request. Supports standard Oracle URI patterns and can include service name or SID.</td><td style="word-wrap: break-word;">oracle://system:<password>@db.example.com:1521/XEPDB1</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the Oracle data connector operation before failing.</td><td style="word-wrap: break-word;">60</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the Oracle database information.</td><td style="word-wrap: break-word;">Oracle Database Info: Query Results (1 rows): version: 19c \| instance_name: XE \| current_user: SYSTEM</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON response containing the Oracle database info.</td><td style="word-wrap: break-word;">{"version":"19c","instance_name":"XE","current_user":"SYSTEM","service_name":"XEPDB1"}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of results. Not provided by this node (empty string).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX of results. Not provided by this node (empty string).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF of results. Not provided by this node (empty string).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials URI required**: You must supply a valid Oracle URI (for example, with service name or SID).
- **Output formats**: This node returns only text and JSON outputs; HTML, XLSX, and PDF are intentionally empty.
- **Service configuration**: The Oracle data connector endpoint must be configured in the environment; otherwise the request will fail.
- **Timeouts**: Long-running operations may need a higher timeout value.
- **Optional custom service URL**: If a service_url query parameter is present in the URI, it can override the default endpoint.

## Troubleshooting
- **Invalid credentials URI**: If you see an error about an invalid URI, ensure it starts with oracle:// and includes host, port, and service name or SID.
- **Service URL is not configured**: Verify that the Oracle data connector endpoint is set in the environment configuration or pass a service_url in the URI.
- **Request timed out**: Increase the timeout input to accommodate slow networks or large environments.
- **Authentication failed**: Check username/password in the URI and ensure the database user has permission to access instance metadata.
- **Unexpected response format**: Ensure the target service version matches the node expectation; retry or contact an administrator if the response lacks expected fields.
