# MySQL List Databases

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node connects to a MySQL server using a stored MySQL credential and invokes a backend "/list-databases" operation. It retrieves all databases that the authenticated user is allowed to see and formats the response as both a readable text summary and a structured JSON object. This makes it easy to plug database discovery into larger data or analytics workflows without writing SQL.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqllistdatabases.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover which databases are available on a MySQL server before choosing one for further operations. It fits early in a pipeline, after you have configured and selected a MySQL credential, and before nodes that require a specific database name such as "SaltMySQLListTables", "SaltMySQLTableInfo", or "SaltMySQLVisualQuery". A common pattern is: (1) select credentials, (2) run "SaltMySQLTestConnection" to verify connectivity, (3) run "SaltMySQLListDatabases" to enumerate databases, and (4) pass the resulting database names into a selection or branching node that decides which database to use. The JSON output can drive dynamic UI choices or automated logic (for example, iterate over all non-system databases and run checks using `SaltMySQLListTables`). In production workflows, align the timeout with expected network latency and ensure the underlying MySQL user has permissions to list databases; otherwise, you may see empty results or permission errors.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier or path to the stored MySQL credential configuration. This must reference a credential created with the MySQL template ("mysql") containing host, port, username, password or other auth method, and any required SSL settings. The user behind this credential must have privileges to list databases on the target server.</td><td style="word-wrap: break-word;">secrets/mysql/prod-reporting-cluster</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of seconds to wait for the backend "/list-databases" request to complete before failing. Must be a positive integer. Use higher values for remote or heavily loaded servers; very small values can cause frequent timeouts in normal conditions.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the databases found, suitable for logs or display. Typically includes a header such as "MySQL Databases" plus a bullet list or count of database names.</td><td style="word-wrap: break-word;">MySQL Databases: - information_schema - mysql - performance_schema - app_core - app_analytics</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured JSON representation of the list-databases result. Contains an array of database entries and may include additional metadata such as server connection details or status. Use this output for programmatic filtering, selection, or iteration in downstream nodes.</td><td style="word-wrap: break-word;">{'status': 'success', 'databases': [{'name': 'information_schema'}, {'name': 'mysql'}, {'name': 'performance_schema'}, {'name': 'sys'}, {'name': 'app_core'}, {'name': 'app_analytics'}], 'server': {'host': 'mysql-prod.internal', 'port': 3306, 'user': 'reporting_user'}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node relies on a backend service call plus a MySQL query; network latency, VPNs, and server load can significantly affect runtime. Increase the `timeout` value for cross-region or internet-facing databases.
- **Limitations**: The list of databases is limited to what the configured MySQL user can see. If the user lacks `SHOW DATABASES` or equivalent privileges, results may be empty or incomplete even if more databases exist.
- **Behavior**: Credentials are resolved from `credentials_path` at execution time. Updating the underlying credential (for example, rotating a password or changing host) immediately affects this node in all workflows without modifying the node configuration.
- **Behavior**: The JSON payload is designed to be stable but may include extra metadata fields added by the backend over time. Downstream logic should focus on the `databases` array and access additional fields defensively rather than assuming a rigid schema.

## Troubleshooting
- **Common Error 1**: "Authentication failed" or "Access denied for user". Check that the credential at `credentials_path` is valid (host, port, username, password) and that the client IP used by Salt is allowed by the MySQL server. Use `SaltMySQLTestConnection` to verify connectivity with the same credentials.
- **Common Error 2**: "Timeout while calling /list-databases" or similar timeout messages. Increase the `timeout` input, verify network connectivity (VPN, firewall, security groups), and confirm that the MySQL server is responsive and not under heavy load.
- **Common Error 3**: The node runs successfully but returns an empty or very short database list. Confirm the privileges of the MySQL user; it must be able to list databases. Test with a more privileged account or grant `SHOW DATABASES` permissions and re-run.
- **Common Error 4**: Errors about loading or finding credentials (for example, "unable to load credentials"). Ensure `credentials_path` points to an existing stored credential configured with the `mysql` template and that the current Salt project or environment has permission to use it.
