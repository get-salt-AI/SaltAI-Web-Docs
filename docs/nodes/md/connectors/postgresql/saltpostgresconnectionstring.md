# PostgreSQL Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Constructs a PostgreSQL connection URI from discrete parameters (host, port, database, username, password, schema, sslmode). It concatenates these inputs into a standard postgresql:// URL and appends optional query parameters when specified.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need a correctly formatted PostgreSQL connection string for downstream database nodes or external tools. Provide your server details and credentials; the node outputs the URI in the text slot, ready to pass to connection or query nodes.

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
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Database port number (1–65535).</td><td style="word-wrap: break-word;">5432</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target database name.</td><td style="word-wrap: break-word;">app_db</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database user name.</td><td style="word-wrap: break-word;">postgres</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Database password for the provided user.</td><td style="word-wrap: break-word;"><postgres-password></td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Default schema to target. If set to "public", it will not be added to the URI as a parameter.</td><td style="word-wrap: break-word;">public</td></tr>
<tr><td style="word-wrap: break-word;">sslmode</td><td>True</td><td style="word-wrap: break-word;">["disable", "require", "verify-ca", "verify-full"]</td><td style="word-wrap: break-word;">SSL mode for the connection. Append as a query parameter if not "disable".</td><td style="word-wrap: break-word;">require</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The constructed PostgreSQL connection string in the form postgresql://username:password@host:port/database with optional ?schema= and/or &sslmode= parameters.</td><td style="word-wrap: break-word;">postgresql://postgres:<postgres-password>@db.example.com:5432/app_db?schema=analytics&sslmode=require</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Empty output for this node (not used).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Empty output for this node (not used).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Empty output for this node (not used).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Empty output for this node (not used).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Password safety**: Do not expose real passwords in logs, prompts, or shared outputs. Use placeholders like "<postgres-password>" in examples.
- **Schema handling**: The schema parameter is only appended when it is not "public".
- **SSL mode**: The sslmode parameter is appended only when not "disable". Choose the correct mode according to your server's TLS configuration.
- **Special characters**: If the username or password contains special characters (e.g., @, /, :, ?), URL-encode them before use to avoid invalid URIs.
- **Validation**: This node only constructs the URI; it does not validate connectivity or credentials.

## Troubleshooting
- **Cannot connect using the URI**: Verify the host, port, and database are reachable and correct. Ensure firewall and network rules allow access.
- **Authentication failed**: Confirm the username/password are correct for the target database. If special characters are present, URL-encode them.
- **SSL errors**: If the server requires TLS, set sslmode to "require" or stricter. For certificate validation, use "verify-ca" or "verify-full" and ensure certificates are configured on the client.
- **Wrong default schema used**: If queries hit the wrong schema, set the schema input to the desired value (not "public") so it is appended to the connection string.
