# MySQL Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a MySQL database connection URI from individual parameters (host, port, database, username, password) with optional charset and SSL mode. Returns the full connection string in the text output slot; other outputs are left empty. This node does not attempt to connect to the database, it only formats the URI.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a properly formatted MySQL connection string to pass into other database nodes or store as configuration. Typical workflow: provide your server details and credentials here, then feed the resulting URI to nodes that execute queries or tests.

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
<tr><td style="word-wrap: break-word;">host</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database server hostname or IP address.</td><td style="word-wrap: break-word;">localhost</td></tr>
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">TCP port the MySQL server listens on.</td><td style="word-wrap: break-word;">3306</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target database name to connect to.</td><td style="word-wrap: break-word;">mysql</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Username for database authentication.</td><td style="word-wrap: break-word;">root</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Password for database authentication. This value is inserted into the URI.</td><td style="word-wrap: break-word;"><db-password></td></tr>
<tr><td style="word-wrap: break-word;">charset</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Character set to use. If left as the default 'utf8mb4', it will not be added to the URI.</td><td style="word-wrap: break-word;">utf8mb4</td></tr>
<tr><td style="word-wrap: break-word;">ssl_mode</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">SSL connection mode. Only added to the URI when different from the default.</td><td style="word-wrap: break-word;">PREFERRED</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The constructed MySQL connection URI in the form mysql://username:password@host:port/database[?charset=...&ssl_mode=...].</td><td style="word-wrap: break-word;">mysql://root:<db-password>@localhost:3306/mysql?ssl_mode=REQUIRED</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Empty output for this node.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Empty output for this node.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Empty output for this node.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Empty output for this node.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Security**: The password is embedded in the URI. Avoid logging or displaying the resulting string in unsecured contexts.
- **Conditional parameters**: 'charset' is only appended if it is not 'utf8mb4'; 'ssl_mode' is only appended if it is not 'PREFERRED'.
- **No connectivity check**: This node does not verify the connection; it only formats the string.
- **Encoding**: If your username or password contains special characters (e.g., @, :, /, ?), URL-encode them to ensure a valid URI.
- **Defaults**: Defaults are host=localhost, port=3306, database=mysql, username=root, charset=utf8mb4, ssl_mode=PREFERRED.

## Troubleshooting
- **Malformed URI or errors returned**: Ensure host, port, and database are correct and that username/password are URL-encoded if they contain special characters.
- **Unexpected missing parameters in URI**: Remember that charset=utf8mb4 and ssl_mode=PREFERRED are omitted by design; change them if you need them included.
- **Downstream node cannot connect**: This node only formats the URI. Verify network access, credentials, and SSL settings in the environment where the downstream node executes.
- **Port out of range**: Port must be between 1 and 65535.
