# PostgreSQL Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Constructs a PostgreSQL connection URI from individual parameters. It concatenates host, port, database, username, and password into a standard postgresql:// URI and appends optional sslmode and schema query parameters when provided.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a ready-to-use PostgreSQL connection string to pass into downstream nodes, services, or configuration fields. It is ideal for workflows that collect connection details interactively and then require a single DSN-style string.

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
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Database port number.</td><td style="word-wrap: break-word;">5432</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target database name.</td><td style="word-wrap: break-word;">my_database</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database user name for authentication.</td><td style="word-wrap: break-word;">db_user</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Password for the provided database user.</td><td style="word-wrap: break-word;"><password></td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema name to include as a query parameter. If set to the default 'public', it will be omitted from the URI.</td><td style="word-wrap: break-word;">analytics</td></tr>
<tr><td style="word-wrap: break-word;">sslmode</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SSL connection mode to include as a query parameter. If 'disable', it will be omitted from the URI.</td><td style="word-wrap: break-word;">require</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The constructed PostgreSQL connection URI.</td><td style="word-wrap: break-word;">postgresql://db_user:<password>@db.example.com:5432/my_database?sslmode=require&schema=analytics</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Empty string (not used by this node).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Empty string (not used by this node).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Empty bytes (not used by this node).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Empty bytes (not used by this node).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Security**: The returned URI includes the password in plain text; avoid displaying or logging it. Use secure storage/handling.
- **Encoding**: If your username or password contains special characters, they may need URL-encoding to form a valid URI.
- **Optional params**: The 'sslmode' and 'schema' parameters are appended only if different from their defaults ('disable' and 'public' respectively).
- **Connectivity**: This node does not validate or test the connection; errors will surface when a downstream process attempts to connect.
- **Schema behavior**: Not all drivers honor a 'schema' query parameter. You may need to set search_path or driver-specific options if the schema is not applied.

## Troubleshooting
- **Downstream connection fails**: Verify host, port, database, username, and password are correct and that the database is reachable from your environment.
- **Authentication errors**: Check for typos and ensure any special characters in credentials are URL-encoded.
- **SSL issues**: If the server requires SSL, set 'sslmode' to 'require' or stricter ('verify-ca', 'verify-full'). Ensure certificates are configured as required by your client.
- **Schema not applied**: If objects are not found in the intended schema, confirm whether your client honors the 'schema' query parameter. Consider setting the search_path or using a driver-specific parameter.
