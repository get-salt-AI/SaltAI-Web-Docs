# SQL Server Table Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves structural metadata for a specific Microsoft SQL Server table. Returns a concise summary and machine‑readable details such as column names, data types, nullability, and related table properties. Designed to help you quickly inspect schemas before building queries or pipelines.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqltableinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to understand a table’s structure before querying or transforming data. Typical workflow: provide credentials, pick a schema (often 'dbo'), specify the table name, run to get columns and types, then feed the results into downstream nodes (e.g., query builders, validators, or data mapping steps).

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored MSSQL credentials that the node will use to authenticate.</td><td style="word-wrap: break-word;"><path-to-mssql-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before timing out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the SQL Server table to inspect.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database schema containing the table. Defaults to 'dbo' if unsure.</td><td style="word-wrap: break-word;">dbo</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human‑readable summary of the table (e.g., number of columns, key columns, and data types).</td><td style="word-wrap: break-word;">Table 'dbo.users' has 8 columns. Primary key: id. Notable columns: email (VARCHAR(255), NOT NULL), created_at (DATETIME).</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw metadata for the table, including columns, data types, nullability, default values, and other properties.</td><td style="word-wrap: break-word;">{"table":"users","schema":"dbo","columns":[{"name":"id","type":"INT","nullable":false,"is_primary_key":true},{"name":"email","type":"VARCHAR(255)","nullable":false}]}</td></tr>
<tr><td style="word-wrap: break-word;">table</td><td style="word-wrap: break-word;">DATAFRAME</td><td style="word-wrap: break-word;">Tabular representation of the table’s columns and properties for easy downstream processing.</td><td style="word-wrap: break-word;">A table-like structure with columns: [name, type, nullable, default, is_primary_key].</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Ensure your credentials grant metadata access to the specified schema and table.
- If the schema is omitted or incorrect, the node may not find the table. Use 'dbo' when in doubt.
- Large or complex schemas can increase response time; adjust the timeout accordingly.
- This node does not read actual row data; it only returns schema/metadata details.

## Troubleshooting
- Table not found: Verify schema and table_name are correct and exist in the target database.
- Authentication failed: Confirm the credentials file is valid and has permission to access the database metadata.
- Timeouts: Increase the timeout input and confirm network connectivity to the SQL Server instance.
- Empty or partial metadata: Ensure the account has sufficient privileges (VIEW DEFINITION or equivalent) on the schema/table.
