# PostgreSQL Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a PostgreSQL database connection URI from individual parameters. It constructs a postgresql://... URL and appends optional query parameters for schema and SSL mode when provided.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a ready-to-use PostgreSQL connection string for downstream database operations. Provide host, port, database, username, and password, and optionally set schema and SSL mode. The resulting URI can be fed into nodes or services that accept PostgreSQL connection strings.

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
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Database port number. Valid range is 1–65535.</td><td style="word-wrap: break-word;">5432</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target database name.</td><td style="word-wrap: break-word;">postgres</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database username.</td><td style="word-wrap: break-word;">postgres</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Database password for the provided user.</td><td style="word-wrap: break-word;"><db-password></td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Default schema name to include as a query parameter. If left as 'public', it is omitted from the URI.</td><td style="word-wrap: break-word;">public</td></tr>
<tr><td style="word-wrap: break-word;">sslmode</td><td>True</td><td style="word-wrap: break-word;">["disable", "require", "verify-ca", "verify-full"]</td><td style="word-wrap: break-word;">SSL connection mode. If 'disable', it is omitted from the URI.</td><td style="word-wrap: break-word;">require</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The constructed PostgreSQL connection string.</td><td style="word-wrap: break-word;">postgresql://postgres:<db-password>@localhost:5432/postgres?sslmode=require</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Empty output for this node.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Empty output for this node.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Empty output for this node.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Empty output for this node.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **No connectivity check**: The node only constructs the URI; it does not validate or test the database connection.
- **Optional parameters omission**: 'schema' is only appended if not 'public'. 'sslmode' is only appended if not 'disable'.
- **URL encoding**: If your username or password contains special characters (e.g., @, :, /, ?), ensure they are URL-encoded to avoid malformed URIs.
- **Security**: Do not expose passwords in shared logs or UI. Treat the generated connection string as sensitive.

## Troubleshooting
- **Downstream connection failures**: Verify host, port, database, username, and password. Confirm network/firewall access to the database.
- **Malformed URI errors**: URL-encode special characters in username or password before constructing the string.
- **SSL errors from clients**: Adjust 'sslmode' to match your server configuration (e.g., 'require', 'verify-ca', 'verify-full').
- **Unexpected schema behavior**: If the target client does not honor the 'schema' parameter, configure the default schema in the client or session explicitly.
- **Empty outputs other than text**: This node only populates the first output with the connection string; other outputs are intentionally empty.
