# SQL Server Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes a SQL Server SELECT query and returns the results. It formats a human-readable summary and provides the raw JSON response. Export-oriented fields (HTML, XLSX, PDF) are not produced by this node and will be empty.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqlquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to read data from a Microsoft SQL Server database using a SELECT statement. Provide a valid SQL Server connection URI in credentials_path, set an optional timeout, and enter the SQL query in sql_text. Typically used early in a workflow to fetch data for analysis, transformation, or downstream processing.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database connection URI for SQL Server. Must be a valid URI such as mssql:// or sqlserver:// including host, port, database, and optionally username/password.</td><td style="word-wrap: break-word;">mssql://sa:<password>@db.example.com:1433/mydatabase</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the query request to complete.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL SELECT query to execute against the SQL Server database.</td><td style="word-wrap: break-word;">SELECT TOP 10 * FROM users</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A formatted, human-readable summary of the query results, including row count and row details.</td><td style="word-wrap: break-word;">Query Results (10 rows): id: 1 \| name: Alice \| email: alice@example.com id: 2 \| name: Bob \| email: bob@example.com</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON response as a string containing the query data and metadata (e.g., data array, row_count).</td><td style="word-wrap: break-word;">{"data": [{"id": 1, "name": "Alice"}, {"id": 2, "name": "Bob"}], "row_count": 2}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of the results. For this node, this field is not generated and will be an empty string.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX of the results. For this node, this field is not generated and will be an empty string.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF of the results. For this node, this field is not generated and will be an empty string.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials URI**: credentials_path must be a valid database URI (e.g., mssql://user:pass@host:port/database or sqlserver://...).
- **Authentication**: If using SQL authentication, ensure username and password are included in the URI. Do not include real secrets in shared projects.
- **Timeouts**: Long-running queries may exceed the timeout; increase timeout or optimize the query.
- **Permissions**: The connected user must have privileges to read the referenced tables or views.
- **Output formats**: Only the text summary and JSON are returned; HTML/XLSX/PDF outputs are empty.

## Troubleshooting
- **Invalid credentials URI**: If you see a validation error, ensure the URI starts with mssql:// or sqlserver:// and includes host, port, and database.
- **Connection or timeout errors**: Verify network access to the database host and port, confirm firewall rules, and increase the timeout value if necessary.
- **Authentication failures**: Check that the username/password or integrated security configuration is correct for the target server.
- **No data returned**: Confirm the SQL query is correct and the target table contains rows; try simplifying the query to test connectivity.
- **Permission denied**: Ensure the database user has SELECT privileges on the target schema/table.
