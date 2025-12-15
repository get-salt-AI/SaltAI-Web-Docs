# PostgreSQL Table Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves structural information for a specific PostgreSQL table. It loads credentials, connects to the database, and returns a human-readable summary along with a JSON payload describing the table. Typical details include columns, data types, and other metadata.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgrestableinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to inspect a table before writing queries or building data pipelines. It’s commonly used alongside List Tables to discover available tables and Visual Query/Query Builder nodes to design queries informed by column metadata.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to PostgreSQL credentials configured for the service.</td><td style="word-wrap: break-word;"><path-to-postgres-credentials.json></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the operation before failing.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the table to retrieve information for.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema where the table resides.</td><td style="word-wrap: break-word;">public</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Readable summary of the table information.</td><td style="word-wrap: break-word;">Table Info: public.users - id: integer (PK) - email: text (unique) - created_at: timestamp without time zone</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded table information suitable for programmatic use.</td><td style="word-wrap: break-word;">{"data": [{"column": "id", "data_type": "integer", "is_nullable": false, "is_primary_key": true}, {"column": "email", "data_type": "text", "is_nullable": false, "is_unique": true}]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML output (not populated by default for this node).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Excel output (not populated by default for this node).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">PDF output (not populated by default for this node).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node requires valid PostgreSQL credentials; ensure your credentials file or reference is correctly configured.
- The default schema is 'public'; adjust the schema input if your table resides elsewhere.
- The output includes both a readable summary (text) and a machine-friendly JSON string.
- Returned metadata can vary depending on database permissions and server configuration.
- Timeouts will fail the operation if the database does not respond within the specified time.

## Troubleshooting
- Authentication or connection errors: Verify credentials_path and network access to the database.
- Table not found: Confirm the table_name and schema are correct and that the table exists.
- Insufficient privileges: Ensure the database user has permissions to read table metadata.
- Timeouts: Increase the timeout value or check database performance/connectivity.
- Empty or partial metadata: Confirm that system catalog access is permitted and that the user has adequate privileges.
