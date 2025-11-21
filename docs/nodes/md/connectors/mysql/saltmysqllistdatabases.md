# SaltMySQLListDatabases

Lists all databases available on a MySQL server using the provided connection URI. The node calls the MySQL data connector service and returns results in multiple formats, including a readable summary and raw JSON.

## Usage

Use this node to discover what databases exist on a target MySQL instance before selecting a specific database for table listing or querying. Typically placed early in a workflow after setting up credentials, and before nodes that operate on specific databases or tables.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">MySQL connection URI containing host, port, database (optional), and credentials. Used to derive service routing and authenticate the request.</td><td style="word-wrap: break-word;">mysql://user:password@db.example.com:3306</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the MySQL data connector service to respond.</td><td style="word-wrap: break-word;">60</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the databases found on the server.</td><td style="word-wrap: break-word;">MySQL Databases - information_schema - mysql - performance_schema - app_db</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON string with the full response payload from the service.</td><td style="word-wrap: break-word;">{"databases": ["information_schema", "mysql", "performance_schema", "app_db"]}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML table representation of the database list for display or rendering.</td><td style="word-wrap: break-word;"><table><tr><th>Database</th></tr><tr><td>information_schema</td></tr><tr><td>mysql</td></tr></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX file containing the list of databases.</td><td style="word-wrap: break-word;"><base64-xlsx-data></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF file containing the list of databases.</td><td style="word-wrap: break-word;"><base64-pdf-data></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials URI required**: You must supply a valid MySQL URI (e.g., mysql://user:password@host:port).
- **Service endpoint configuration**: The MySQL data connector service URL must be configured in the platform endpoints; otherwise, the request will fail.
- **Network and permissions**: The service must be able to reach the MySQL host and the provided user must have permission to list databases.
- **Timeouts**: Long-running or slow network operations may require increasing the timeout value.
- **Security**: Never hard-code real passwords in public workflows; use secure credential management.

## Troubleshooting
- **Invalid credentials URI**: If you see an error about an invalid credentials URI, ensure it starts with mysql:// and includes valid host and optional port.
- **Service URL not configured**: If the error indicates the service URL is missing, verify that the MySQL data connector endpoint is properly set in the environment configuration.
- **Request failed or timed out**: Increase the timeout value, verify network connectivity to the MySQL host, and ensure the service is running and reachable.
- **Empty results**: If no databases are returned, confirm the user has SHOW DATABASES privileges and the server is accessible.
- **Authentication errors**: Double-check username/password in the URI; ensure special characters are URL-encoded.
