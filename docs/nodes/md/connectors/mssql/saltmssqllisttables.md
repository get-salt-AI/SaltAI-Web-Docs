# SQL Server List Tables

Lists all table names within a specified SQL Server schema. It uses provided MSSQL connection details to contact the database service and returns both a readable summary and a JSON payload containing the tables.

## Usage

Use this node to explore or validate what tables exist in a given schema before running queries or building downstream workflows. Provide a valid MSSQL connection URI and the schema name (e.g., dbo). You can construct the URI manually or by using the SQL Server Connection String node, then feed it here.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">MSSQL connection URI containing host, port, database, and authentication details.</td><td style="word-wrap: break-word;">mssql://sa:<password>@localhost:1433/master</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation to complete.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database schema name to list tables from.</td><td style="word-wrap: break-word;">dbo</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable list of tables found in the specified schema.</td><td style="word-wrap: break-word;">Tables in schema 'dbo' (3 tables): - users - orders - products</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing raw results, typically including an array of table names under the 'tables' key.</td><td style="word-wrap: break-word;">{"tables": ["users", "orders", "products"]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials URI required**: The credentials_path must be a valid database URI (e.g., mssql://user:<password>@host:port/database).
- **Schema scope**: Only tables within the specified schema are listed.
- **Service configuration**: Requires the SQL Server data connector service endpoint to be configured in the environment.
- **Timeout bounds**: Typical allowed range is 10–300 seconds; adjust based on network and database load.
- **Security**: Do not hardcode real passwords in workflows; use secure storage or a separate node to assemble the connection string.

## Troubleshooting
- **Invalid credentials URI**: If you see an error about an invalid URI, ensure the format is correct (scheme, host, port, database, and credentials).
- **No tables returned**: Verify the schema name is correct and that the database contains tables in that schema.
- **Connection failures**: Check network connectivity, firewall rules, and that the MSSQL service is reachable from the runtime environment.
- **Service URL not configured**: If you get a service URL configuration error, ensure the SQL Server data connector endpoint is set in the platform settings.
- **Timeouts**: Increase the timeout value if the database is slow or the schema contains a very large number of tables.
