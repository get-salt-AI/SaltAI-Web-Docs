# MySQL List Tables

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lists all tables available in a specified MySQL database using the provided credentials. Returns a human-readable summary and a machine-readable JSON payload you can pass to downstream nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqllisttables.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to explore or enumerate tables in a MySQL database before building queries or fetching table metadata. It’s typically placed early in a workflow to discover available tables, then chained to nodes like MySQL Table Info or MySQL Query.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored MySQL credentials configured for this service.</td><td style="word-wrap: break-word;">/workspace/credentials/mysql/<credentials-file>.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the operation before it times out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The name of the database from which to list tables.</td><td style="word-wrap: break-word;">analytics_db</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Readable summary of the tables found in the specified database.</td><td style="word-wrap: break-word;">Tables in Database: analytics_db - users - orders - products (3 tables)</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the list of tables and related metadata (if available).</td><td style="word-wrap: break-word;">{"tables": [{"name": "users"}, {"name": "orders"}, {"name": "products"}]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: You must have a valid MySQL credential configuration available to the node.
- **Permissions**: The connected user must have privileges to read metadata for the target database; otherwise, the table list may be incomplete or empty.
- **Timeouts**: Large schemas or slow networks may require increasing the timeout.
- **Database name**: Ensure the database exists and is spelled exactly as configured on the server.

## Troubleshooting
- **Connection failed or timed out**: Verify the credentials path, host/port in your credentials, and increase the timeout if the database is large or network is slow.
- **Authentication error**: Confirm username/password or authentication method in the stored credentials and that access to the target database is granted.
- **Unknown database**: Check that the database value matches an existing database the user can access.
- **Empty list returned**: Ensure the user has metadata permissions and that the database contains tables.
