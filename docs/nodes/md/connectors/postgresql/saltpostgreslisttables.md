# PostgreSQL List Tables

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lists all table names within a specified PostgreSQL schema. It authenticates using your configured PostgreSQL credentials and queries the Salt data service to return a formatted list and a JSON payload.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgreslisttables.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover or verify the tables available in a particular PostgreSQL schema before building queries or downstream logic. Typical workflows place this node ahead of query-building or schema-inspection steps to programmatically enumerate available tables.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored PostgreSQL credentials that follow the 'postgres' credential template. The credentials are used to authenticate with the data service.</td><td style="word-wrap: break-word;"><path-to-postgres-credentials.json></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the service to respond before failing the request.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database schema name to list tables from.</td><td style="word-wrap: break-word;">public</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the tables found in the specified schema.</td><td style="word-wrap: break-word;">Tables in Schema: public - users - orders - products</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON response with the table list and related metadata.</td><td style="word-wrap: break-word;">{"data": [{"table_name": "users"}, {"table_name": "orders"}, {"table_name": "products"}]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials**: Requires a valid PostgreSQL credential set that matches the 'postgres' template.
- **Default schema**: If unsure, use 'public' as the schema; many deployments default to it.
- **Timeouts**: Large catalogs or slow networks may require increasing the timeout to avoid failures.
- **Output structure**: The text is a readable summary; the JSON contains the underlying data for programmatic use.

## Troubleshooting
- **Authentication error**: Verify the credentials_path points to a valid file and that it uses the 'postgres' credential template.
- **Connection/timeout issues**: Increase the timeout value or ensure network access to the data service is available.
- **Empty results**: Confirm the schema name is correct and that the database user has permissions to list tables.
- **Unexpected JSON format**: Inspect the JSON output for 'error' fields; correct the schema or credentials and retry.
