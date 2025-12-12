# PostgreSQL Table Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed metadata for a specific PostgreSQL table within a given schema. Returns a human-readable summary along with a JSON payload you can pass to downstream nodes. Typical details include columns, data types, nullability, primary keys, and other structural information.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgrestableinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to inspect or document a table’s structure before building queries or transformations. Commonly placed early in a workflow to validate schema assumptions, plan JOINs, or assist with visual/AI-assisted query building.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored PostgreSQL credentials configured for this service.</td><td style="word-wrap: break-word;">/workspace/credentials/postgres.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before failing.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the table to fetch metadata for.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database schema containing the target table.</td><td style="word-wrap: break-word;">public</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Readable summary of the table structure and key attributes.</td><td style="word-wrap: break-word;">Table Info: public.users Columns: - id (integer, not null, primary key) - email (text, not null) - created_at (timestamp with time zone, not null) ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-serialized metadata for the table, suitable for programmatic use.</td><td style="word-wrap: break-word;">{"table":"users","schema":"public","columns":[{"name":"id","type":"integer","nullable":false,"primary_key":true},{"name":"email","type":"text","nullable":false}],"indexes":[...]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Ensure the provided credentials have sufficient permissions to read schema metadata for the specified table and schema.
- The schema name must be valid and the table must exist; otherwise the node returns an error message and an error JSON.
- Timeout applies to the metadata retrieval request; large schemas or slow connections may require increasing this value.
- This node focuses on structural information, not table data rows.

## Troubleshooting
- Table not found: Verify the 'table_name' and 'schema' values and confirm the table exists.
- Permission denied: Confirm the database user in the credentials has privileges to access information_schema and the target table.
- Connection or credential errors: Check 'credentials_path' formatting, host, port, database, username, and password in your stored credentials.
- Request timed out: Increase 'timeout' and ensure network/database performance is adequate.
- Empty or partial metadata: Confirm the target database supports metadata queries and that the user can access all required catalog views.
