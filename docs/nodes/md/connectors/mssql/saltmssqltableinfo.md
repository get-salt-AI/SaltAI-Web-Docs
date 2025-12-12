# SQL Server Table Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves metadata for a specific Microsoft SQL Server table, including column names, data types, nullability, and common attributes like length, precision, scale, and default values. Returns a human-readable summary plus the raw JSON payload from the service.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqltableinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to inspect the structure of a SQL Server table before building queries or pipelines. Typical workflows include schema discovery, validation of expected columns and types, and documenting table structures for downstream processing or integrations.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database connection URI for SQL Server. Must be a valid URI format.</td><td style="word-wrap: break-word;">mssql://db_user:<password>@db-host.example.com:1433/YourDatabase</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Request timeout in seconds for the operation.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the table to retrieve information about.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema that contains the target table.</td><td style="word-wrap: break-word;">dbo</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the table structure (schema-qualified table name and per-column details).</td><td style="word-wrap: break-word;">Table: dbo.users Column: id \| Type: int \| Nullable: NO \| Precision: 10 Column: email \| Type: varchar \| Nullable: NO \| Max Length: 255 \| Default: '' Column: created_at \| Type: datetime2 \| Nullable: YES</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON string containing the table metadata returned by the service.</td><td style="word-wrap: break-word;">{"columns": [{"column_name": "id", "data_type": "int", "is_nullable": "NO", "numeric_precision": 10}, {"column_name": "email", "data_type": "varchar", "is_nullable": "NO", "character_maximum_length": 255}]}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of results when available. For this operation, this will be empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX export when available. For this operation, this will be empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF export when available. For this operation, this will be empty.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials URI is required**: The credentials_path must be a valid SQL Server URI (e.g., mssql://user:<password>@host:1433/database).
- **Schema must exist**: Ensure the schema provided (default is dbo) exists and the user has privileges to read metadata.
- **Read-only operation**: This node reads table metadata only; it does not modify database data.
- **Timeout applies to the remote call**: Increase the timeout if your database or network is slow.
- **Output formats**: For this operation, only the text summary and JSON payload are populated; HTML/XLSX/PDF are returned as empty strings.

## Troubleshooting
- **Invalid credentials URI**: If you see an error about an invalid URI, verify the scheme (mssql://), host, port, database name, and that special characters in the password are properly encoded.
- **Table not found**: Confirm the table_name and schema are correct and exist in the target database.
- **Permission denied**: If results are empty or access is denied, ensure the database user has metadata/INFORMATION_SCHEMA read permissions.
- **Network/service unreachable**: If the operation times out or fails to connect, check network connectivity, firewall rules, and that the database service and required endpoints are accessible.
- **Empty columns list**: Verify the database user’s permissions and that the table is not a view with restricted metadata visibility.
