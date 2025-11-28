# PostgreSQL Table Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves structural information about a PostgreSQL table (e.g., columns, data types, constraints) using the configured database credentials. Returns a human-readable summary along with the raw JSON metadata. Useful for exploring schema details before writing queries or building data pipelines.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgrestableinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to inspect a table’s schema in a PostgreSQL database. Provide the credentials file path, select the target schema and table, and run the node to get both a formatted summary and the underlying JSON metadata. This is typically used ahead of crafting queries, validating integrations, or documenting data models.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored credentials configuration for the PostgreSQL service. The credentials must match the 'postgres' credential template.</td><td style="word-wrap: break-word;">/workspace/credentials/postgres.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before timing out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the table to retrieve information about.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the database schema where the table resides.</td><td style="word-wrap: break-word;">public</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Formatted, human-readable summary of the table structure.</td><td style="word-wrap: break-word;">Table Info: public.users - Columns:   id (integer, not null)   email (text, not null)   created_at (timestamp, null) - Primary Key: users_pkey (id) - Indexes: users_email_idx (email) - Foreign Keys: ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON metadata returned by the service detailing columns, data types, constraints, and other table properties.</td><td style="word-wrap: break-word;">{"data": {"columns": [{"name": "id", "type": "integer", "nullable": false}], "primary_key": ["id"], "indexes": [], "foreign_keys": []}}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML rendition of the results if available; otherwise an empty string.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary Excel content if exported; otherwise empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF content if exported; otherwise empty.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials**: Requires a valid PostgreSQL credential configuration (credential template: "postgres").
- **Schema and table names**: The default schema is "public". Ensure the specified table exists in the given schema.
- **Permissions**: The database user in the provided credentials must have sufficient privileges to read metadata.
- **Timeouts**: Operations respect the specified timeout; large or slow systems may require higher values.
- **Outputs**: The node returns a formatted text summary and the raw JSON. Other output formats may be empty if not generated.

## Troubleshooting
- **Authentication failed**: Verify credentials_path points to a valid file and that the credentials match the target database.
- **Table not found**: Check spelling, schema selection, and case sensitivity for the table_name and schema.
- **Insufficient privileges**: Ensure the user has metadata access (e.g., to information_schema or catalog views).
- **Network/connection issues**: Confirm the database is reachable from your environment and firewall rules allow access.
- **Timeouts**: Increase the timeout input if the database is slow or under heavy load.
