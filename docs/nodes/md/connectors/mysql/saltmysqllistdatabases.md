# MySQL List Databases

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lists all accessible databases on a MySQL server using the provided credentials. It sends a request to the MySQL service and returns both a human-readable summary and a JSON payload of the databases.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqllistdatabases.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the beginning of a database workflow to discover which databases are available for a given MySQL connection. Commonly chained with nodes that require a database name (e.g., listing tables or fetching table info) after selecting one of the returned databases.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored MySQL credentials configured for Salt. Must reference a credential that matches the 'mysql' template.</td><td style="word-wrap: break-word;">path/to/credentials/mysql.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before timing out.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the databases found.</td><td style="word-wrap: break-word;">MySQL Databases: information_schema, mysql, performance_schema, sys</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured JSON containing the list of databases and any metadata returned by the service.</td><td style="word-wrap: break-word;">{"data": [{"database": "mysql"}, {"database": "information_schema"}]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML output (empty for this node by default).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Binary Excel data (empty for this node by default).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">Binary PDF data (empty for this node by default).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: Ensure a valid MySQL credential (matching the 'mysql' template) is configured and referenced by credentials_path.
- **Access permissions**: The returned databases are limited to what the provided user is allowed to see.
- **Timeouts**: Long-running or unreachable database endpoints may result in a timeout; adjust the timeout value as needed.
- **Outputs**: This node primarily returns text and JSON. HTML/XLSX/PDF outputs are not populated for this operation.

## Troubleshooting
- **Authentication failure**: Verify the credentials_path points to a valid MySQL credential and that username/password are correct.
- **No databases returned**: Check user permissions and ensure the account has privileges to list databases.
- **Connection/timeout errors**: Confirm network access to the MySQL host and increase the timeout if the server is slow to respond.
- **Unexpected JSON structure**: If downstream nodes expect a specific JSON shape, inspect the json output to align field paths.
