# SQL Server Table Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves metadata for a specific Microsoft SQL Server table, including column names, data types, nullability, and common constraints. It formats a concise, readable summary and also returns the raw JSON metadata.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqltableinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to inspect the structure of a SQL Server table before building queries or performing data operations. Typical workflow: provide a valid SQL Server connection URI, specify the schema and table name, then connect its outputs to documentation, validation, or query-building steps.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL Server connection URI containing host, port, database, and credentials.</td><td style="word-wrap: break-word;">mssql://username:<password>@db.example.com:1433/mydatabase?instance=SQLEXPRESS</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Request timeout in seconds for the database service call.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The table name to describe.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The database schema where the table resides.</td><td style="word-wrap: break-word;">dbo</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable table structure summary, listing columns with data types and attributes.</td><td style="word-wrap: break-word;">Table: dbo.users Column: id \| Type: INT \| Nullable: NO \| Precision: 10 \| Default: IDENTITY(1,1) Column: email \| Type: VARCHAR \| Nullable: NO \| Max Length: 255 ...</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON string with table metadata (columns and their properties).</td><td style="word-wrap: break-word;">{"columns": [{"column_name": "id", "data_type": "int", "is_nullable": "NO", "column_default": "identity(1,1)"}, {"column_name": "email", "data_type": "varchar", "is_nullable": "NO", "character_maximum_length": 255}]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Ensure the credentials_path is a valid SQL Server URI (e.g., mssql://user:<password>@host:1433/database).
- Schema is required; if unsure, use "dbo" which is the common default schema in SQL Server.
- This node returns table metadata only; it does not return table rows.
- If using Windows Integrated Security, include the appropriate parameter in the URI (e.g., mssql://host:1433/database?integrated_security=true).
- Network and service endpoint configuration must be set up beforehand; the node calls a configured database service endpoint.

## Troubleshooting
- Invalid credentials URI: Verify the URI format and that it begins with mssql:// or sqlserver:// and includes required parts (host, port, database).
- Authentication failed: Check username/password or integrated_security settings in the URI.
- Table not found: Confirm the schema and table_name are correct and the user has permissions to access them.
- Schema not found: Ensure the provided schema exists in the target database.
- Timeout or connection error: Increase the timeout value and verify network connectivity and service endpoint availability.
- Empty or unexpected metadata: Confirm the connected service supports metadata retrieval for the target database and that your user has metadata inspection privileges.
