# PostgreSQL Connection String

Builds a PostgreSQL database connection URI from individual parameters. It concatenates host, port, database, username, and password, and conditionally appends schema and SSL options as query parameters. Designed as a helper to generate a ready-to-use connection string for downstream database nodes or external tools.

## Usage

Use this node when you need a correctly formatted PostgreSQL connection string from separate inputs (e.g., for plugging into other database nodes or services). Typical workflow: provide your server details and credentials, choose SSL mode if needed, and pass the generated URI to nodes that accept a connection string.

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
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Database port number. Must be between 1 and 65535.</td><td style="word-wrap: break-word;">5432</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target database name.</td><td style="word-wrap: break-word;">postgres</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database user name.</td><td style="word-wrap: break-word;">postgres</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Database user password. Keep this secure.</td><td style="word-wrap: break-word;"><password></td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Default schema to use. If set to 'public', it will not be added to the URI query.</td><td style="word-wrap: break-word;">public</td></tr>
<tr><td style="word-wrap: break-word;">sslmode</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">SSL connection mode. Only added to the URI if not 'disable'.</td><td style="word-wrap: break-word;">require</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The constructed PostgreSQL connection string (URI).</td><td style="word-wrap: break-word;">postgresql://postgres:<password>@localhost:5432/postgres?sslmode=disable</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON output (unused for successful construction; may contain error details on failure).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML output (not used by this node; empty on success).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">XLSX output (not used by this node; empty on success).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF output (not used by this node; empty on success).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- The schema parameter is appended only when it is not 'public'.
- The sslmode parameter is appended only when it is not 'disable'.
- This node does not validate database connectivity; it only formats a connection string.
- If your username or password contains special characters, ensure they are URL-encoded before use.
- Handle secrets carefully. Avoid exposing passwords in logs, shared graphs, or UI screenshots.

## Troubleshooting
- If downstream nodes fail to connect, verify host, port, database, username, and password are correct, and that the server is reachable.
- If authentication issues occur, confirm that sslmode matches your server's SSL requirements (e.g., 'require', 'verify-ca', or 'verify-full').
- If special characters in credentials cause parsing errors, URL-encode the username and password.
- If you see an error message in the JSON output slot, check for invalid or missing input values.
