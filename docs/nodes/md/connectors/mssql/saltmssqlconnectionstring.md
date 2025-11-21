# SQL Server Connection String

Builds a Microsoft SQL Server (MSSQL) database connection URI from individual parameters. Supports both SQL Server authentication and Windows Integrated Security, and optionally includes a named instance in the query string.

## Usage

Use this node when you need to assemble a valid MSSQL connection string to pass into other database nodes or workflows that require a single URI. Typical flow: supply host, port, database, and either username/password or enable integrated security; the node returns a ready-to-use mssql:// URI.

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
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">TCP port for SQL Server.</td><td style="word-wrap: break-word;">1433</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target database name.</td><td style="word-wrap: break-word;">master</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL Server username. Ignored if integrated_security is true.</td><td style="word-wrap: break-word;">sa</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">SQL Server password for the specified user. Ignored if integrated_security is true.</td><td style="word-wrap: break-word;"><password></td></tr>
<tr><td style="word-wrap: break-word;">instance</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional SQL Server named instance. When provided (and not using integrated security), it is appended as a query parameter.</td><td style="word-wrap: break-word;">MSSQLSERVER</td></tr>
<tr><td style="word-wrap: break-word;">integrated_security</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Enable Windows Integrated Security. If true, username and password are not included; the connection string will include integrated_security=true.</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The constructed MSSQL connection string. On success, contains the mssql:// URI; on error, contains a human-readable error message.</td><td style="word-wrap: break-word;">mssql://sa:<password>@db.example.com:1433/mydb</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded details. For this node, it is typically empty on success. On error, contains a JSON object with "error".</td><td style="word-wrap: break-word;">{"error": "Failed to construct connection string: <reason>"}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Not used by this node; returned as an empty string.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Not used by this node; returned as an empty string.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Not used by this node; returned as an empty string.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Authentication behavior**: If integrated_security is true, the URI is built as mssql://host:port/database?integrated_security=true and ignores username/password and instance.
- **Instance handling**: When using SQL authentication and an instance is provided, the URI becomes mssql://user:pass@host:port/database?instance=<instance>.
- **Special characters**: The node does not URL-encode credentials. Usernames or passwords containing reserved URI characters (e.g., @, :, /, ?, #, &) may break the URI. Prefer using credentials without special characters or encode them before input.
- **Output format**: Only the 'result' output is meaningful on success. 'json_result' is only populated on error; other outputs are empty strings.
- **Defaults**: Default port is 1433 and default database is 'master' if not changed.

## Troubleshooting
- **Malformed URI due to special characters**: If the resulting URI looks broken or downstream nodes reject it, ensure username/password do not contain characters like @, :, /, ?, #, &. If unavoidable, provide URL-encoded values.
- **Using Integrated Security but still seeing username/password in the URI**: Set integrated_security to true; this removes credentials and adds integrated_security=true.
- **Connection fails downstream due to wrong port or host**: Verify host and port (default 1433) match your SQL Server configuration and that firewall rules allow access.
- **Named instance not connecting**: Ensure the instance name is correct. If your environment resolves instances via SQL Browser service, confirm it’s running or provide the correct port.
- **Empty json_result on failure expectations**: Only errors populate json_result. If you need structured output for success, parse the 'result' string instead.
