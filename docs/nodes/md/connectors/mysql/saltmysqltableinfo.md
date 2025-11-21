# SaltMySQLTableInfo

Retrieves metadata for a specific MySQL table. It calls the MySQL service to return details like columns, data types, primary keys, and other schema information, and formats the response as human-readable text and JSON.

## Usage

Use this node when you need to inspect the structure of a MySQL table before building queries, data pipelines, or validations. Provide the target database and table name; the node will query the service for schema details and output a descriptive summary along with the raw JSON payload for downstream processing.

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
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The name of the table to retrieve information for.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The MySQL database containing the table.</td><td style="word-wrap: break-word;">analytics_db</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">TEXT</td><td style="word-wrap: break-word;">A formatted summary of the table metadata, including table name and key structural details.</td><td style="word-wrap: break-word;">Table Info: analytics_db.users Columns: id (INT, PK), email (VARCHAR), created_at (DATETIME)...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">The raw table metadata returned by the service, suitable for parsing and downstream automation.</td><td style="word-wrap: break-word;">{"table":"users","database":"analytics_db","columns":[{"name":"id","type":"INT","primary_key":true},{"name":"email","type":"VARCHAR"}]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: Valid MySQL credentials must be available via the node's credential configuration for the request to succeed.
- **Accurate identifiers**: Ensure the database and table names are correct and accessible with provided credentials.
- **Service-backed**: The node communicates with a backend service; network connectivity and service availability can affect results.
- **Timeouts**: Long-running metadata calls in large schemas may require increasing the timeout.

## Troubleshooting
- **Table not found**: Verify the 'database' and 'table_name' values and confirm the user has permissions to read schema metadata.
- **Authentication errors**: Check that 'credentials_path' points to a valid configuration and that credentials are active.
- **Connection/timeout failures**: Increase 'timeout', confirm network access to the service, and retry.
- **Empty or partial metadata**: Ensure the account has sufficient privileges (e.g., SHOW/SELECT metadata permissions) on the target schema.
