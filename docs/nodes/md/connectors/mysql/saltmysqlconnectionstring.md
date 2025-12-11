# MySQL Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a MySQL database connection URI from individual parameters. Outputs the fully constructed connection string in the text output slot; all other outputs are empty. Optional charset and SSL mode are appended as query parameters only when they differ from defaults.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a valid MySQL connection string to pass into downstream nodes or external systems. Provide host, port, database, username, and password, and (optionally) override charset or SSL mode. The resulting URI appears in the text output.

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
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database username.</td><td style="word-wrap: break-word;">report_user</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Database password. Will be embedded in the URI.</td><td style="word-wrap: break-word;"><db-password></td></tr>
<tr><td style="word-wrap: break-word;">charset</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Character set for the connection. Only appended if different from utf8mb4.</td><td style="word-wrap: break-word;">utf8mb4</td></tr>
<tr><td style="word-wrap: break-word;">ssl_mode</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">SSL connection mode. Only appended if different from PREFERRED.</td><td style="word-wrap: break-word;">REQUIRED</td></tr>
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
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Empty for this node.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Empty for this node.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Empty for this node.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Empty for this node.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Password exposure**: The password is included directly in the URI; handle and store it securely.
- **URL encoding**: Special characters in username or password are not automatically URL-encoded. Manually encode them if needed.
- **Optional parameters**: charset is appended only when not 'utf8mb4'; ssl_mode only when not 'PREFERRED'.
- **Output location**: The connection string is returned in the text output; all other outputs are intentionally empty.
- **Format**: The URI format is mysql://username:password@host:port/database with optional query parameters.

## Troubleshooting
- **Authentication failures**: Verify username/password and that credentials are correctly embedded. URL-encode special characters if present.
- **Connection refused or timeout**: Confirm host and port are reachable and correct; ensure firewalls/security groups allow access.
- **Unknown database**: Make sure the database name exists and the user has access.
- **SSL errors**: If using a strict ssl_mode (e.g., VERIFY_CA/VERIFY_IDENTITY), verify server certificates and CA configuration.
- **Invalid charset**: Use a valid MySQL charset (e.g., utf8mb4); incorrect values may cause connection issues in consumers.
