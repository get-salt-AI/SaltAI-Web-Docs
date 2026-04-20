# MySQL Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node constructs a MySQL connection string (URI) from individual parameters such as host, port, database, username, and password. It optionally appends charset and SSL mode as query parameters when they differ from defaults, producing a reusable MySQL URI for downstream components.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a clear, reusable MySQL connection string instead of manually writing URLs. Common scenarios include: (1) generating a URI for custom integration or script nodes that accept a MySQL URL, (2) maintaining distinct connection settings for dev/stage/prod environments by parameterizing host, port, and database, and (3) documenting or logging the effective connection configuration for audits or debugging. In a typical workflow, place this node near the start where database configuration is chosen; its output is then passed into custom code, orchestration, or external systems that consume a MySQL URI. It complements other MySQL nodes in this module (SaltMySQLQuery, SaltMySQLExecute, SaltMySQLTableInfo, SaltMySQLListTables, SaltMySQLListDatabases, SaltMySQLTestConnection) that mainly rely on credential files, by offering a portable string representation of the same connection details. Best practice is to avoid hardcoding real secrets, align ssl_mode with production security requirements (for example, REQUIRED or VERIFY_CA), and only override charset when you truly need something other than utf8mb4.

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
<tr><td style="word-wrap: break-word;">host</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Hostname or IP address of the MySQL server. This can be a DNS name (for example, db.internal.company.com) or an IP address. The node does not check connectivity; it simply embeds this value into the URI, so it must be correct for whatever client will use the string.</td><td style="word-wrap: break-word;">db-read-replica.internal.company.com</td></tr>
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">TCP port on which the MySQL server is listening. Must be between 1 and 65535. Defaults to 3306, the standard MySQL port, but can be changed for custom deployments.</td><td style="word-wrap: break-word;">3306</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the MySQL database (schema) to connect to. This becomes the path portion of the URI and must match an existing database on the server for clients to connect successfully.</td><td style="word-wrap: break-word;">analytics_prod</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">MySQL username used for authentication. It is placed directly before the @ in the URI. Use a least-privilege account with only the permissions needed by downstream operations.</td><td style="word-wrap: break-word;">etl_service_user</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Password for the specified MySQL user. It is embedded directly into the URI. Because this may expose secrets in logs or shared configurations, rely on secure secret management and do not store real passwords in shared diagrams or exports.</td><td style="word-wrap: break-word;"><mysql-user-password></td></tr>
<tr><td style="word-wrap: break-word;">charset</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Character set for the connection. Default is utf8mb4. When left as utf8mb4, it is treated as the default and not appended to the URI; when set to another value, it is added as a charset query parameter.</td><td style="word-wrap: break-word;">utf8mb4</td></tr>
<tr><td style="word-wrap: break-word;">ssl_mode</td><td>True</td><td style="word-wrap: break-word;">["DISABLED","PREFERRED","REQUIRED","VERIFY_CA","VERIFY_IDENTITY"]</td><td style="word-wrap: break-word;">SSL connection mode for MySQL. PREFERRED (default) will use SSL if available but does not require it. DISABLED forces a non-SSL connection. REQUIRED enforces SSL, while VERIFY_CA and VERIFY_IDENTITY additionally validate the CA certificate and hostname. Any value other than PREFERRED is appended to the URI as an ssl_mode query parameter.</td><td style="word-wrap: break-word;">REQUIRED</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The constructed MySQL connection URI in the form mysql://username:password@host:port/database, optionally followed by query parameters such as charset and ssl_mode. This is the primary output used by downstream components that accept a MySQL connection string.</td><td style="word-wrap: break-word;">mysql://etl_service_user:<mysql-user-password>@db-read-replica.internal.company.com:3306/analytics_prod?ssl_mode=REQUIRED</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">An empty string on successful construction. If an error occurs while building the URI, this field contains a JSON-encoded error object explaining the failure.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Always an empty string for this node in normal operation. Included only for structural compatibility with other database nodes that may output HTML.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Always empty for this node. Present solely for interface compatibility with nodes that export tabular data to Excel; connection strings do not produce spreadsheet content.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Always empty for this node. Included for compatibility with nodes that export PDF reports; this node does not generate PDFs.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node only assembles a string and does not touch the network or database, so it executes essentially instantly with negligible resource usage.
- **Limitations**: Username and password are inserted into the URI without URL encoding. If they contain characters such as @, :, ?, or #, some clients may misinterpret the URI unless those values are URI-safe or pre-encoded.
- **Behavior**: The charset and ssl_mode parameters are only added to the URI when they differ from their defaults (utf8mb4 and PREFERRED), keeping the URI concise when using standard settings.
- **Behavior**: On failure, the node returns a human-readable error message in the text output and a JSON error object in the json output, while html, xlsx, and pdf remain empty. Downstream logic should treat non-URI text output as an error state.

## Troubleshooting
- **Client cannot connect using the URI**: Confirm that host, port, database, username, and password match your MySQL configuration and that the user has appropriate permissions on the specified database.
- **Malformed URI due to special characters**: If credentials containing characters like @, :, or # cause parsing errors, adjust the credentials to URI-safe values or URL-encode them before using the connection string.
- **SSL-related connection failures**: If the server enforces SSL and connections fail, set ssl_mode to REQUIRED, VERIFY_CA, or VERIFY_IDENTITY. If SSL is not supported in your environment and connections fail unexpectedly, try DISABLED to see if SSL negotiation is the issue.
- **Missing charset or ssl_mode in URI**: If you expect charset or ssl_mode parameters but they are absent, verify that you changed them from their defaults (utf8mb4 and PREFERRED). Only non-default values are appended as query parameters.
