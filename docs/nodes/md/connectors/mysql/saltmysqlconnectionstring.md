# MySQL Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a MySQL connection URI from individual parameters. It assembles host, port, database, username, and password, and optionally appends charset and SSL mode as query parameters. The result is a single connection string suitable for use in downstream database operations.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a properly formatted MySQL connection string constructed from separate input fields. Typical workflow: provide your database server details and credentials, then pass the resulting URI to nodes or services that accept a MySQL connection string.

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
<tr><td style="word-wrap: break-word;">host</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database hostname or IP address.</td><td style="word-wrap: break-word;">localhost</td></tr>
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Database port number.</td><td style="word-wrap: break-word;">3306</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the target database.</td><td style="word-wrap: break-word;">mydb</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database username.</td><td style="word-wrap: break-word;">dbuser</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Database password.</td><td style="word-wrap: break-word;"><mysql-password></td></tr>
<tr><td style="word-wrap: break-word;">charset</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Character set for the connection. If left as the default, it will be omitted from the URI.</td><td style="word-wrap: break-word;">utf8mb4</td></tr>
<tr><td style="word-wrap: break-word;">ssl_mode</td><td>True</td><td style="word-wrap: break-word;">DISABLED \| PREFERRED \| REQUIRED \| VERIFY_CA \| VERIFY_IDENTITY</td><td style="word-wrap: break-word;">SSL connection mode. If left as the default (PREFERRED), it will be omitted from the URI.</td><td style="word-wrap: break-word;">PREFERRED</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The constructed MySQL connection string in URI form.</td><td style="word-wrap: break-word;">mysql://dbuser:<mysql-password>@localhost:3306/mydb?charset=utf8mb4</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON metadata or error details. Empty on success; contains {"error": "..."} on failure.</td><td style="word-wrap: break-word;">{"error": "Failed to construct connection string: ..."}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Unused for this node. Returns empty string.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Unused for this node. Returns empty bytes.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Unused for this node. Returns empty bytes.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Charset and SSL mode are appended as query parameters only when different from their defaults (utf8mb4 for charset, PREFERRED for ssl_mode).
- Special characters in username, password, or database are not URL-encoded by this node; ensure values are URI-safe or pre-encode them.
- The output focuses on providing the connection URI in the first output; other outputs are empty unless an error occurs, in which case the second (json) output includes error details.
- This node does not load or reference external stored credentials; it builds the URI strictly from the provided inputs.

## Troubleshooting
- Connection string contains unexpected characters: URL-encode username, password, or database if they include special characters (e.g., @, :, /, ?).
- Downstream connection fails with SSL errors: adjust the ssl_mode to match your server configuration (e.g., REQUIRED, VERIFY_CA, or VERIFY_IDENTITY).
- Authentication fails: verify username/password and ensure the user has access to the specified database and host.
- Port or host unreachable: confirm network connectivity, firewall rules, and correct host/port values.
- Received an error message in the json output: review the error text for clues (e.g., formatting or missing inputs), correct the inputs, and retry.
