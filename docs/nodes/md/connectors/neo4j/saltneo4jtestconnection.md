# Neo4J Test Connection

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Validates connectivity to a Neo4J database using the provided credentials. It performs a non-destructive health check and returns a readable status plus a JSON payload with details. Useful for confirming host, port, authentication, and SSL settings before running queries.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jtestconnection.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a workflow to ensure your Neo4J credentials and network access are correct. If the test passes, proceed with query, write, or schema operations. If it fails, inspect the returned JSON for error details and adjust credentials or connection parameters.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored Neo4J credentials configured for this environment.</td><td style="word-wrap: break-word;"><path-to-neo4j-credentials.json></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the connection test to complete.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the connection test result.</td><td style="word-wrap: break-word;">Neo4J Connection Test\nStatus: OK\nLatency: 120 ms</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Machine-readable JSON payload with connection test details (e.g., status, errors, server info).</td><td style="word-wrap: break-word;">{"status": "ok", "server_version": "5.x", "latency_ms": 120}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML rendition of the results (may be empty if not requested).</td><td style="word-wrap: break-word;"><table><tr><td>Status</td><td>OK</td></tr></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">XLSX export of the results (may be empty if not requested).</td><td style="word-wrap: break-word;"><binary-xlsx-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF export of the results (may be empty if not requested).</td><td style="word-wrap: break-word;"><binary-pdf-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node requires valid Neo4J credentials to be configured and accessible via credentials_path.
- The operation is read-only and does not modify the database.
- Timeout applies to the connection check request; very short timeouts may cause false negatives on slow networks.
- If using encrypted protocols, ensure certificates and trust settings in your credentials are correct.

## Troubleshooting
- Authentication failed: Verify username/password in the credentials and ensure the account has access.
- Connection timed out: Check host/port, firewall rules, VPN/proxy settings, and increase the timeout.
- TLS/SSL errors: Adjust protocol (e.g., bolt+s/neo4j+s), trust settings, or certificates in your credentials.
- Protocol/port mismatch: Use the correct protocol and port (e.g., bolt on 7687, HTTP endpoints are not supported here).
- Service unreachable: Confirm the Neo4J server is running and accessible from the environment executing this node.
