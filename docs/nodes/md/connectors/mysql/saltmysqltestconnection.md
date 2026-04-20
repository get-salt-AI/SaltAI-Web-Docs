# MySQL Test Connection

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node validates that your MySQL credentials are correct and that the Salt MySQL service can successfully connect to the target database. It loads a MySQL credential set, invokes a backend "/test-connection" operation, and returns both a readable status summary and a JSON-encoded diagnostic payload. Use it as a guard step before executing queries or schema operations that depend on stable connectivity.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqltestconnection.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Place the MySQL Test Connection node early in any workflow that interacts with MySQL, especially after configuring or updating database credentials. A typical pattern is: configure credentials in Salt, reference them via credentials_path, run this node, and if it reports success, proceed to operational nodes such as SaltMySQLQuery, SaltMySQLExecute, SaltMySQLTableInfo, SaltMySQLListTables, SaltMySQLListDatabases, or SaltMySQLVisualQuery. If the connection test fails, branch your workflow to handle the failure (for example, send an alert, write to a log, or skip later database steps). The node uses the same underlying database base layer as other CData-backed MySQL nodes, so timeout handling and result formatting are consistent. It works well alongside SaltMySQLConnectionString, which can help you construct connection URIs that are then embedded in the credential configuration this node tests.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or identifier for the stored MySQL credential configuration. This must reference a Salt credential entry that uses the mysql template and defines the necessary connection information, such as host, port, database, username, password, and any SSL parameters used by the backend test-connection operation.</td><td style="word-wrap: break-word;">secrets/mysql/prod_reporting_cluster</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds that the node will wait for the MySQL test-connection request to complete. Use a larger value for remote or heavily loaded servers and a smaller value for fast local databases. Must be a positive integer and should align with expected network latency and server responsiveness.</td><td style="word-wrap: break-word;">20</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the connection test, indicating success or failure and often including brief diagnostic information such as the target host or a short error message. Suitable for log messages, UI display, or manual review.</td><td style="word-wrap: break-word;">MySQL Connection Test: SUCCESS Connected to mysql-reporting.internal:3306 as user 'report_user'.</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded diagnostic data returned by the backend test-connection operation. Common fields include a status indicator, optional error description, and basic metadata like host, port, and database. Downstream nodes can parse this JSON to implement conditional logic, for example halting the pipeline when status is not "success".</td><td style="word-wrap: break-word;">{"status":"success","host":"mysql-reporting.internal","port":3306,"database":"reports","latency_ms":55}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional HTML representation of the connection test result, produced by the shared formatting logic. It can be embedded in dashboards, reports, or notification emails for visually formatted status. For simple use cases this output may be left empty.</td><td style="word-wrap: break-word;"><h3>MySQL Connection Test</h3><p>Status: <strong>SUCCESS</strong></p><p>Database: reports</p></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Optional binary Excel (XLSX) data that may contain a tabular representation of connection diagnostics. Many workflows will not use this output for a simple connectivity check, but it is available for consistency with other database nodes that support exporting diagnostics or results to spreadsheets.</td><td style="word-wrap: break-word;">UEsDBBQABgAIAAAAIQAAAAAAAAAAAAAAAAAJAAAAdGVzdC54bWx...</td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Optional binary PDF data containing a formatted connection report suitable for archiving or compliance documentation. It is usually empty in basic monitoring workflows but can be used when a PDF artifact of the connection test is required.</td><td style="word-wrap: break-word;">JVBERi0xLjQKJcTl8uXrp/Og0MTGCjEgMCBvYmoKPDwvVHlwZS9DYXRhbG9n...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: While the test is lightweight, connection establishment and authentication still incur network and server overhead; tune timeout values to match your environment to avoid premature failures or excessive waits.
- **Limitations**: A passing test confirms reachability and credential validity but does not guarantee that the user has required permissions for all queries, schema changes, or large data operations you might perform later.
- **Behavior**: The node uses a shared database base layer that wraps driver and network behavior into a consistent text and JSON output format; low-level exceptions are not exposed directly, so you should inspect the json output for precise error details.
- **Behavior**: The node strictly relies on the configuration referenced by credentials_path; any changes to server endpoints, firewall rules, or passwords must be reflected in that credential entry or the connection test will begin to fail, even if other parts of your infrastructure have been updated.

## Troubleshooting
- **Authentication or access errors**: If the text output indicates authentication failed or access denied, verify that the credentials at credentials_path have the correct username and password and that the account is permitted to connect from the Salt environment’s network.
- **Host or network errors**: If the output mentions unknown host, DNS failure, or network unreachable, confirm that the host and port in your credential configuration are correct, that DNS resolves properly, and that firewalls or security groups are not blocking the connection.
- **Repeated timeouts**: When the node consistently times out, first increase the timeout value and rerun; if the issue persists, investigate network latency, server load, or any rate limiting on the MySQL server, and ensure the database is actually listening on the configured port.
- **SSL or certificate problems**: If the json output contains SSL or certificate errors, check that the mysql credential template’s SSL options match your server’s configuration, such as required versus optional SSL and the correct CA certificates, and update the credential resource accordingly.
