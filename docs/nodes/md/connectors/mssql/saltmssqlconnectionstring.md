# SQL Server Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a valid Microsoft SQL Server (MSSQL) database URI from individual parameters. Supports SQL authentication (username/password), optional instance names, and Windows Integrated Security. Returns the connection string in the primary output and leaves other outputs empty; on error, returns an error message and JSON error details.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqlconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to construct a MSSQL connection URI to pass into other SQL Server nodes (Query, Execute, etc.) or any component that expects a credentials_path URI. Typical workflow: configure host, port, database, and either username/password or enable integrated security; then feed the resulting connection string into subsequent database nodes.

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
<tr><td style="word-wrap: break-word;">host</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database server hostname or IP address.</td><td style="word-wrap: break-word;">db.example.com</td></tr>
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">TCP port for the SQL Server instance. Defaults to 1433.</td><td style="word-wrap: break-word;">1433</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target database name.</td><td style="word-wrap: break-word;">SalesDB</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Username for SQL authentication. Ignored if Integrated Security is enabled.</td><td style="word-wrap: break-word;">sa</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Password for SQL authentication. Ignored if Integrated Security is enabled.</td><td style="word-wrap: break-word;"><db-password></td></tr>
<tr><td style="word-wrap: break-word;">instance</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional SQL Server instance name. If provided, it is appended as a query parameter.</td><td style="word-wrap: break-word;">MSSQLSERVER</td></tr>
<tr><td style="word-wrap: break-word;">integrated_security</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Enable Windows Integrated Security (no username/password in the URI).</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The constructed MSSQL connection string or an error message if construction failed.</td><td style="word-wrap: break-word;">mssql://sa:<db-password>@db.example.com:1433/SalesDB?instance=MSSQLSERVER</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON error details if construction fails; otherwise empty.</td><td style="word-wrap: break-word;">{"error": "Failed to construct connection string: <reason>"}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Not used by this node. Always empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Not used by this node. Always empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Not used by this node. Always empty.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- If Integrated Security is enabled, the URI format is mssql://<host>:<port>/<database>?integrated_security=true and username/password are ignored.
- If an instance name is provided, it is appended as ?instance=<instance> to the URI.
- Special characters in usernames or passwords are not URL-encoded by this node. If your credentials contain special characters, pre-encode them to avoid malformed URIs.
- This node does not validate or test the database connection; it only constructs the URI.
- Default port is 1433 if you are unsure of the SQL Server port.

## Troubleshooting
- Got an authentication error downstream: Ensure you used the correct authentication mode. If using Integrated Security, turn it on and leave username/password unused; otherwise provide valid SQL credentials.
- Connection fails due to special characters: URL-encode username/password before input (e.g., replace special characters with their percent-encoded form).
- Server requires an instance name: Provide the correct instance in the 'instance' field so it is included as a query parameter.
- Downstream node rejects credentials_path: Verify the output begins with mssql:// and includes host, port, and database as expected.
