# SQL Server List Tables

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lists all tables available in a specified SQL Server schema using the provided database credentials. Returns a human-readable summary and a structured JSON payload of the tables. Useful for quickly discovering table names before running queries or exploring schema contents.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqllisttables.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover or verify the tables present in a particular SQL Server schema (e.g., dbo) as part of a data exploration, validation, or query-building workflow. Provide a valid SQL Server credential configuration and the target schema; the node returns both a text overview and machine-readable JSON that can be passed to downstream nodes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored SQL Server credentials file configured for the 'mssql' service. Must contain host, port, database, and authentication details.</td><td style="word-wrap: break-word;">/data/credentials/mssql.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the list operation to complete before failing.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL Server schema name to enumerate tables from.</td><td style="word-wrap: break-word;">dbo</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the tables found in the specified schema.</td><td style="word-wrap: break-word;">Tables in schema dbo: - users - orders - products</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded list of tables with optional metadata returned by the service.</td><td style="word-wrap: break-word;">[{"table": "users"}, {"table": "orders"}, {"table": "products"}]</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML-formatted table or empty string if not provided for this operation.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary Excel data or empty string if not provided for this operation.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF data or empty string if not provided for this operation.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: Ensure the credentials file at credentials_path is valid and configured for the 'mssql' service.
- **Schema permissions**: The database user must have sufficient privileges to read metadata for the specified schema; otherwise, results may be empty or fail.
- **Timeout**: Large schemas or slow connections may require increasing the timeout value to avoid premature failures.
- **Output formats**: Primary outputs are text and JSON. HTML/XLSX/PDF outputs may be empty for this operation.
- **Environment configuration**: If your SQL Server uses named instances or integrated security, ensure your credentials reflect those settings.

## Troubleshooting
- **Connection failed**: Verify host, port, database, username/password (or integrated security) in the credentials file and confirm network/firewall access.
- **Empty results**: Check that the schema name is correct and that the user has metadata read permissions for that schema.
- **Permission denied**: Ask your database administrator to grant VIEW DEFINITION or equivalent metadata permissions on the target schema.
- **Slow or timed out**: Increase the timeout input and try again; also verify server load and network latency.
- **Unexpected schema**: If you intended to list a different schema, update the 'schema' input (e.g., 'sales', 'hr', or 'dbo').
