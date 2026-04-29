# Oracle Test Connection

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node performs a lightweight connectivity check against an Oracle database using stored Oracle credentials. It calls the Oracle service's test-connection endpoint and wraps the response using the standard database node formatter, producing human-readable text plus structured JSON. Use it to confirm that host, port, service/SID, and authentication are working before any query or metadata operations.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracletestconnection.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Oracle Test Connection node at the start of any workflow that depends on Oracle data, immediately after selecting or providing your Oracle credentials. The node consumes a credentials_path pointing to an Oracle credential template and a timeout value, loads the credentials, and invokes a /test-connection operation on the Oracle integration backend. Typical upstream nodes are credential selection or management nodes that output credentials_path, or helper nodes like SaltOracleConnectionString (if you embed the connection string into a credentials file). Downstream nodes commonly include SaltOracleQuery, SaltOracleVisualQuery, SaltOracleTableInfo, SaltOracleListTables, or SaltOracleDatabaseInfo once connectivity has been verified. A practical workflow pattern is: (1) select or configure Oracle credentials, (2) run SaltOracleTestConnection with an environment-appropriate timeout, (3) on success, proceed to query or schema exploration; on failure, route to logging, alerting, or remediation steps. In scheduled or production jobs, include this node to fail fast on network or configuration issues rather than discovering them after heavier operations.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or identifier to the saved Oracle credential configuration. This should reference an Oracle credential template (template key "oracle") that contains connection details such as host, port, service/SID, username, and password or connection string. The path must be valid and readable by the runtime, and the content must be well-formed and complete for an Oracle connection.</td><td style="word-wrap: break-word;">/workspace/credentials/oracle/hr_reporting_oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the Oracle test-connection request before treating it as failed. Use higher values for remote or high-latency networks (e.g., cross-region, VPN) and lower values for local data centers. Must be a positive integer; overly small values can cause false negatives due to normal network latency.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">text_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the Oracle connection test result, suitable for console display, logs, or status cards in the UI. Usually includes a success/failure status and brief context about the target connection.</td><td style="word-wrap: break-word;">Oracle Connection Test: SUCCESS - Connected to db-prod-01.acme.local:1521 (service: ORCL) in 0.42 seconds.</td></tr>
<tr><td style="word-wrap: break-word;">json_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON document serialized as a string containing the raw response from the /test-connection endpoint and any additional metadata added by the formatter. This typically includes a status field, message, and diagnostic details such as latency, resolved host, and error codes when failures occur.</td><td style="word-wrap: break-word;">{"status": "success", "message": "Connection successful", "details": {"host": "db-prod-01.acme.local", "port": 1521, "service_name": "ORCL", "latency_ms": 420}}</td></tr>
<tr><td style="word-wrap: break-word;">html_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML-formatted version of the test result, generated by the shared database node formatter. This can be rendered directly in web-based dashboards or embedded into reports and notifications.</td><td style="word-wrap: break-word;"><h2>Oracle Connection Test</h2><p>Status: <strong>SUCCESS</strong></p><p>Host: db-prod-01.acme.local:1521</p><p>Service: ORCL</p><p>Latency: 420 ms</p></td></tr>
<tr><td style="word-wrap: break-word;">file_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or identifier for any file-based artifact the formatter produces for this test, if applicable. In many cases this may be an empty string, but some configurations can emit a small JSON or HTML report file for auditing or archiving.</td><td style="word-wrap: break-word;">/workspace/outputs/oracle/connection_tests/2024-05-10T09-30-00Z-oracle-connection-test.json</td></tr>
<tr><td style="word-wrap: break-word;">ui_payload</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Serialized payload (typically JSON encoded as a string) describing how to present the test results in the Salt UI. It may specify component types, status indicators, and key-value fields for display, and can be used by downstream orchestration or monitoring nodes.</td><td style="word-wrap: break-word;">{"type": "status_card", "title": "Oracle Connection Test", "status": "success", "details": {"host": "db-prod-01.acme.local", "service_name": "ORCL", "latency_ms": 420}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Response time depends on network conditions and Oracle server load; for remote databases, pick a timeout that accounts for worst-case latency to avoid spurious failures.
- **Limitations**: The node validates connectivity and basic authentication only; it does not check privileges on individual schemas or tables, nor does it guarantee that subsequent SQL statements will succeed.
- **Behavior**: The node uses the Oracle credential template specified by credentials_path; any mismatch in host, port, service/SID, or credentials in that file will cause the test to fail even if another Oracle environment is healthy.
- **Behavior**: The structure of the JSON response is dictated by the backend /test-connection API and may include additional fields over time; code that parses json_output should handle missing or extra keys gracefully instead of assuming a fixed schema.

## Troubleshooting
- **Connection timed out or host unreachable**: If the node reports a timeout or unreachable host, increase the timeout value and verify network connectivity from the Salt environment to the Oracle host and port. Check firewalls, VPN tunnels, security groups, and that the Oracle listener is running on the expected port.
- **Authentication failed (invalid username/password)**: Errors indicating bad credentials mean the stored username or password in the credentials file is incorrect or expired. Update the credential at credentials_path with correct values and rerun the connection test.
- **Service name or SID errors**: If the error mentions an unknown service name, SID, or similar Oracle listener error, confirm that the service_name or SID in your Oracle configuration matches what the database is actually exposing (for example, ORCL vs XE) and that the credentials file uses the correct value.
- **SSL or protocol-related errors**: Messages about TLS/SSL handshake failures or protocol mismatches often indicate that the Oracle listener is configured differently from what the client expects (for example, SSL required vs plaintext). Validate listener configuration, certificates, and any proxy or load balancer between Salt and the Oracle server, and adjust your Oracle connector settings or credentials accordingly.
