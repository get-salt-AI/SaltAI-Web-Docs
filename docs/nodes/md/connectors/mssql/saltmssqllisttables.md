# SQL Server List Tables

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lists all tables available within a specified SQL Server schema. Uses a provided database URI to connect via the configured SQL Server data connector and returns both a readable list and a JSON payload. Designed for discovery and inspection of database structures.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqllisttables.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to enumerate tables in a given schema before querying or building downstream data workflows. Typical flow: provide a valid SQL Server connection URI, specify the schema (defaults to dbo), optionally adjust the timeout, and pass the outputs to selection or documentation steps.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A SQL Server connection URI used to connect to the database.</td><td style="word-wrap: break-word;">mssql://sa:<password>@localhost:1433/master</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Request timeout in seconds for the database operation.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The database schema to list tables from.</td><td style="word-wrap: break-word;">dbo</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of tables found in the schema.</td><td style="word-wrap: break-word;">Tables in schema 'dbo' (3 tables): - users - orders - products</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string with the raw service response, typically including an array of table names under 'tables'.</td><td style="word-wrap: break-word;">{"tables": ["users", "orders", "products"]}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation. For this operation, this field is typically empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX content. For this operation, this field is typically empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF content. For this operation, this field is typically empty.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- The credentials_path must be a valid database URI and should start with mssql:// or sqlserver://.
- If the target schema has no tables, the node returns a clear 'No tables found' message and an empty list in JSON.
- The schema input defaults to 'dbo' if not changed.
- Timeout is in seconds and applies to the request made to the data connector service.
- Ensure your user has permissions to read metadata for the specified schema; insufficient privileges may result in errors or empty results.
- If using username/password in the URI, never include real secrets in shared documents; use placeholders like <password>.

## Troubleshooting
- Invalid credentials URI: Ensure the URI format is correct, e.g., mssql://user:<password>@host:1433/database.
- Service not configured: If you encounter a 'Service URL is not configured' error, verify that the SQL Server data connector endpoint is set up in your environment.
- Authentication or permission errors: Confirm the provided credentials have rights to access the database and read schema metadata.
- Connection/timeout issues: Increase the timeout value or check network connectivity and firewall settings to the SQL Server and connector service.
- Empty result: Confirm the schema name is correct and that tables exist; also verify the user’s permissions on that schema.
