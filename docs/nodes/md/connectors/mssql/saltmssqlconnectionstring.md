# SQL Server Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a Microsoft SQL Server connection URI from individual parameters. Supports both SQL authentication and Windows Integrated Security, and optionally includes a named instance. Returns the constructed connection string as the primary output.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqlconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to assemble a properly formatted SQL Server connection string from user-provided fields to pass into downstream database nodes or external tools. Typical workflow: provide host, port, database, and either username/password or enable integrated security; the node outputs a single ready-to-use connection URI.

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
<tr><td style="word-wrap: break-word;">host</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database hostname or IP address.</td><td style="word-wrap: break-word;">db.mycompany.internal</td></tr>
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">TCP port for SQL Server. Default is 1433.</td><td style="word-wrap: break-word;">1433</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target database name.</td><td style="word-wrap: break-word;">SalesDB</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL Server login username. Ignored if Integrated Security is enabled.</td><td style="word-wrap: break-word;">sa</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">SQL Server login password. Ignored if Integrated Security is enabled.</td><td style="word-wrap: break-word;"><mssql-password></td></tr>
<tr><td style="word-wrap: break-word;">instance</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Named SQL Server instance. If provided, it is added to the connection string.</td><td style="word-wrap: break-word;">MSSQLSERVER</td></tr>
<tr><td style="word-wrap: break-word;">integrated_security</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Use Windows Integrated Security (true) instead of username/password (false).</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">connection_string</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The constructed MSSQL connection URI. Uses Integrated Security or username/password based on inputs.</td><td style="word-wrap: break-word;">mssql://username:<password>@db.mycompany.internal:1433/SalesDB</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted error details when construction fails; empty on success.</td><td style="word-wrap: break-word;">{"error": "Failed to construct connection string: <reason>"}</td></tr>
<tr><td style="word-wrap: break-word;">out_3</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Unused placeholder output (empty string).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">out_4</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Unused placeholder output (empty string).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">out_5</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Unused placeholder output (empty string).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- If integrated_security is true, username and password are ignored and the connection string includes integrated_security=true.
- When an instance is provided, it is appended as a query parameter (instance=<name>).
- Special characters in usernames or passwords may need URL encoding to ensure a valid URI.
- Only the first output (connection_string) is meaningful on success; other outputs are placeholders.
- On error, the first output contains a human-readable message and the second output contains a JSON-formatted error string.

## Troubleshooting
- Connection string is missing parts: Verify host, port, and database are provided and non-empty.
- Authentication issues downstream: If using Integrated Security, ensure the environment supports it; otherwise set integrated_security to false and provide username/password.
- Invalid characters in password: URL-encode special characters in the password before input.
- Incorrect server/instance resolution: If connecting to a named instance, provide the instance value; otherwise leave it blank and ensure the port is correct.
- Error returned in second output: Inspect the JSON error string for details and correct the offending parameter.
