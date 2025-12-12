# MySQL Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a MySQL database connection URI from individual fields. It concatenates host, port, database, username, and password into a mysql:// URI and conditionally appends query parameters for charset and SSL mode.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to construct a standardized MySQL connection string for downstream nodes that require a single URI input. Typical workflow: supply your database host, port, name, username, and password; optionally adjust charset and SSL mode; feed the resulting connection string into query or connectivity nodes.

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
<tr><td style="word-wrap: break-word;">host</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database hostname or IP address.</td><td style="word-wrap: break-word;">db.example.com</td></tr>
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">TCP port for the MySQL server.</td><td style="word-wrap: break-word;">3306</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target database/schema name.</td><td style="word-wrap: break-word;">analytics</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Username for authentication.</td><td style="word-wrap: break-word;">report_user</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Password for authentication.</td><td style="word-wrap: break-word;"><db-password></td></tr>
<tr><td style="word-wrap: break-word;">charset</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Character set for the connection. Only appended to the URI if not the default.</td><td style="word-wrap: break-word;">utf8mb4</td></tr>
<tr><td style="word-wrap: break-word;">ssl_mode</td><td>True</td><td style="word-wrap: break-word;">['DISABLED', 'PREFERRED', 'REQUIRED', 'VERIFY_CA', 'VERIFY_IDENTITY']</td><td style="word-wrap: break-word;">SSL connection mode. Only appended to the URI if not the default.</td><td style="word-wrap: break-word;">REQUIRED</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The constructed MySQL connection string (URI).</td><td style="word-wrap: break-word;">mysql://report_user:<db-password>@db.example.com:3306/analytics?ssl_mode=REQUIRED</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Empty string for this node (no JSON payload produced).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Empty string for this node (no HTML output produced).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Empty for this node (no spreadsheet output produced).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Empty for this node (no PDF output produced).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Default parameters**: charset defaults to utf8mb4 and ssl_mode defaults to PREFERRED; these are only appended to the URI if changed.
- **Special characters**: Username and password are not URL-encoded by this node. If they contain special characters (e.g., @, :, /, ?), the resulting URI may be invalid.
- **Security**: Avoid logging or sharing the generated URI since it contains credentials.
- **Format**: The base format is mysql://username:password@host:port/database with optional ?charset=... and &ssl_mode=... query parameters.
- **No external credential file**: This node does not require or consume a credentials path; it purely formats a URI from provided inputs.

## Troubleshooting
- **Connection fails using the URI**: Verify host, port, and database name are correct and accessible from your environment.
- **Malformed URI or parsing errors**: Remove or URL-encode special characters in username/password, or use credentials without special characters.
- **SSL errors**: Adjust ssl_mode (e.g., use REQUIRED or VERIFY_CA) to match your server's configuration. If unsure, try PREFERRED.
- **Charset issues (garbled text)**: Set charset to a value supported by your server and client (utf8mb4 is recommended).
