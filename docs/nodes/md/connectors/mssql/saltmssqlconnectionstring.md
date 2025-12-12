# SQL Server Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a Microsoft SQL Server (MSSQL) connection URI from provided parameters. Supports SQL Server authentication and Windows Integrated Security, and optionally includes a SQL Server instance name. Returns the constructed connection string or an error payload if construction fails.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqlconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to programmatically assemble a valid MSSQL connection string for downstream database nodes. Choose Integrated Security for Windows authentication (ignores username/password), or provide username and password for SQL authentication. Optionally include an instance name if your SQL Server is configured with a named instance.

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
<tr><td style="word-wrap: break-word;">host</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database hostname or IP address.</td><td style="word-wrap: break-word;">db.example.local</td></tr>
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Port number for the SQL Server.</td><td style="word-wrap: break-word;">1433</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target database name.</td><td style="word-wrap: break-word;">master</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Username for SQL authentication. Ignored if Integrated Security is enabled.</td><td style="word-wrap: break-word;">sa</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Password for SQL authentication. Ignored if Integrated Security is enabled.</td><td style="word-wrap: break-word;"><db-password></td></tr>
<tr><td style="word-wrap: break-word;">instance</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional SQL Server instance name. If provided, it is added as a query parameter.</td><td style="word-wrap: break-word;">MSSQLSERVER</td></tr>
<tr><td style="word-wrap: break-word;">integrated_security</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Enable Windows Integrated Security. When true, username and password are not used.</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">connection_string</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Constructed MSSQL connection URI. Example format (SQL auth): mssql://username:password@host:port/database; with instance: mssql://username:password@host:port/database?instance=InstanceName; with Integrated Security: mssql://host:port/database?integrated_security=true</td><td style="word-wrap: break-word;">mssql://sa:<db-password>@db.example.local:1433/master</td></tr>
<tr><td style="word-wrap: break-word;">error_json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string describing an error if construction fails; empty string on success.</td><td style="word-wrap: break-word;">{"error": "Failed to construct connection string: <reason>"}</td></tr>
<tr><td style="word-wrap: break-word;">reserved_3</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reserved/unused output. Empty string in normal operation.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">reserved_4</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reserved/unused output. Empty string in normal operation.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">reserved_5</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reserved/unused output. Empty string in normal operation.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Authentication modes**: If integrated_security is true, username and password are ignored and the URI includes ?integrated_security=true.
- **Instance handling**: If an instance is provided, it is appended as ?instance=<InstanceName> to the URI.
- **Special characters**: Usernames/passwords with special characters may require URL encoding to form a valid URI.
- **Security**: Do not share or log connection strings containing credentials. Use secure handling for secrets.
- **Defaults**: Defaults are host=localhost, port=1433, database=master, username=sa, integrated_security=false.

## Troubleshooting
- **Invalid URI due to special characters**: URL-encode username/password or use credentials without reserved URL characters.
- **Authentication errors despite correct values**: Verify integrated_security setting matches the server configuration. For Windows auth, ensure the executing context has appropriate permissions.
- **Connection refused or timeout**: Confirm host and port are reachable, firewall rules allow traffic, and the SQL Server service is running.
- **Named instance not resolving**: Ensure the instance name is correct and SQL Browser service is available, or specify the exact port for the instance.
