# Neo4J Test Connection

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node performs a health-check against a configured Neo4J database using the provided credentials and timeout. It verifies that the database is reachable, credentials are valid, and the backing Neo4J service can establish a session. The result is returned as human-readable text plus structured JSON and related formatted outputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jtestconnection.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to confirm that your Neo4J connection is correctly configured before running any read/write or exploration operations. Place it near the start of workflows that will later call nodes such as "Neo4J Query", "Neo4J Write", "Neo4J Nodes by Label", "Neo4J Relationships by Type", or "Neo4J Graph Explorer" to avoid executing complex logic against an unreachable database. Upstream, you typically supply a `credentials_path` from your configuration or secrets management flow that points to the Neo4J credential template. Downstream, you can route based on success or failure (for example, aborting the pipeline if the connection test fails, logging the JSON output into monitoring systems, or displaying the HTML output in a status dashboard). Because it uses the same backend service and connection template as other Neo4J nodes (via the `/test-connection` endpoint), a successful test is a strong indication that subsequent Neo4J operations will also function correctly. It is especially useful in smoke tests for new environments or after updating credentials, firewall rules, or network routes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or identifier that locates the stored Neo4J credentials used by the CData Neo4J service. This should point to a credential configuration based on the "neo4j" template, including connection URI/host, port, username, password or token, and any required SSL settings. If this reference is invalid or missing required fields, the test will fail before contacting the database.</td><td style="word-wrap: break-word;">config/credentials/databases/neo4j_prod.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of seconds to wait for the connection test to complete. For high-latency networks, remote databases, or heavily loaded instances, you may need to increase this value. If set too low, valid connections may be reported as failures due to timeouts.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the connection test result, indicating whether the test succeeded and, if not, providing a concise error message. Suitable for logs, notifications, and simple conditional checks.</td><td style="word-wrap: break-word;">Neo4J Connection Test: SUCCESS - Connected to neo4j://graph-db.internal:7687 as user 'analytics_user'.</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded details of the test result. Typically includes status (such as success or failure), a message, and may include diagnostic information like latency or server metadata. Downstream nodes that need structured information should parse this JSON.</td><td style="word-wrap: break-word;">{"status": "success", "message": "Connection established", "latency_ms": 120, "database": "neo4j", "server_version": "5.13.0"}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML-formatted representation of the test result, often a small summary block or table. This is intended for dashboards, HTML reports, or any consumer that can render HTML.</td><td style="word-wrap: break-word;"><h3>Neo4J Connection Test</h3><p>Status: <strong>SUCCESS</strong></p><p>Latency: 120 ms</p></td></tr>
<tr><td style="word-wrap: break-word;">file</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reference to a generated file artifact, if any, produced by the shared database formatting utilities (for example, an exported report). For a simple connection test this may be empty in many setups.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional JSON-encoded metadata about the test execution, such as timestamps, service identifiers, or request IDs that can help with debugging and auditing.</td><td style="word-wrap: break-word;">{"timestamp": "2026-04-20T14:12:03Z", "service": "neo4j", "endpoint": "/test-connection"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Overall runtime is dominated by network latency and Neo4J server responsiveness; when testing over VPNs or slow links, increase the timeout to reduce false failures.
- **Limitations**: This node only confirms basic connectivity and authentication; it does not check that the user has all required permissions for specific queries or write operations you may perform later.
- **Behavior**: The node depends on a shared credential template named "neo4j" and a backend `/test-connection` operation; if that backend service is misconfigured or offline, the node will report failures even if the Neo4J instance itself is healthy.
- **Behavior**: All outputs, including JSON and HTML, are returned as strings. Any downstream logic that needs structured data must explicitly parse the JSON output.
- **Limitations**: The node does not execute Cypher or inspect schema; for deeper diagnostics use nodes like "Neo4J Database Info", "Neo4J Schema Info", or "Neo4J Node Labels" in combination with this test.

## Troubleshooting
- **Authentication failures**: If the output indicates invalid credentials (for example, "authentication failed" or similar wording), verify the user, password or token, and Neo4J URI in the file referenced by `credentials_path`, then retest.
- **Timeouts or connection refused**: When you see timeouts or connection-refused errors, raise the `timeout` value and verify network connectivity, firewall rules, and that the Neo4J server is listening on the host and port defined in the credentials.
- **Backend operation not found**: Errors mentioning an unknown or unsupported `/test-connection` endpoint usually point to a misconfigured or outdated backend service; ensure the Neo4J connector service is correctly deployed and compatible with this node.
- **Unexpected or empty JSON result**: If `text` reports an error but `json` is empty or not parseable, treat the test as failed and consult execution logs for the underlying database service or infrastructure, as there may be issues with serialization or error handling.
