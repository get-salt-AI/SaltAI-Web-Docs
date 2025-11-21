# MySQL Connection String

Builds a MySQL connection URI from individual parameters (host, port, database, username, password, charset, SSL mode). It conditionally appends query parameters for charset (if not utf8mb4) and ssl_mode (if not PREFERRED). Output is a single text connection string suitable for passing to downstream nodes or tools.

## Usage

Use this node when you need a correctly formatted MySQL connection string to configure database-related nodes or external services. Provide your database connection details, then connect the text output to nodes that accept a connection string or store it for later use.

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
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Database port number.</td><td style="word-wrap: break-word;">3306</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target database/schema name.</td><td style="word-wrap: break-word;">analytics</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database user name.</td><td style="word-wrap: break-word;">db_user</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Database user password.</td><td style="word-wrap: break-word;"><db-password></td></tr>
<tr><td style="word-wrap: break-word;">charset</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Character set for the connection. If left as utf8mb4, it will not be added to the URI.</td><td style="word-wrap: break-word;">utf8mb4</td></tr>
<tr><td style="word-wrap: break-word;">ssl_mode</td><td>True</td><td style="word-wrap: break-word;">ENUM[DISABLED, PREFERRED, REQUIRED, VERIFY_CA, VERIFY_IDENTITY]</td><td style="word-wrap: break-word;">SSL connection mode. If PREFERRED, it will not be added to the URI; other values will be appended as a query parameter.</td><td style="word-wrap: break-word;">REQUIRED</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The constructed MySQL connection string (URI). Other output channels are empty for this node.</td><td style="word-wrap: break-word;">mysql://db_user:<db-password>@db.example.com:3306/analytics?ssl_mode=REQUIRED</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Empty string for this node.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Empty string for this node.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Empty for this node.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Empty for this node.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Default parameters**: charset=utf8mb4 and ssl_mode=PREFERRED are not included in the URI unless changed.
- **No validation/test**: This node only constructs a string; it does not validate credentials or test the connection.
- **Special characters**: Username/password are not URL-encoded by this node. If they contain special characters (e.g., @, :, /, ?), the resulting URI may be invalid. Prefer credentials without such characters or pre-encode them.
- **URI scheme**: Uses the mysql:// scheme with the format mysql://username:password@host:port/database and optional query parameters.
- **Security**: Avoid exposing the resulting URI in logs or shares as it contains credentials.

## Troubleshooting
- **Connection fails downstream**: Verify host, port, database, credentials, and that the resulting URI matches the target tool's expected format.
- **SSL handshake errors**: Adjust ssl_mode to match your server configuration (e.g., REQUIRED, VERIFY_CA) or set to DISABLED/PREFERRED if appropriate.
- **Authentication errors**: Ensure username/password are correct and consider URL-encoding if they contain special characters.
- **Driver rejects URI**: Some drivers require mysql+pymysql:// or another dialect prefix. Confirm the consumer's expected scheme and adapt if necessary.
