# SQL Server Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes non-SELECT SQL statements (INSERT, UPDATE, DELETE, DDL) against a Microsoft SQL Server database. Uses a connection URI to contact the Salt database service and returns a concise execution summary and a JSON result. Designed for write/administrative operations rather than data retrieval.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqlexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to modify data or schema in a SQL Server database from a Salt workflow. Provide a valid MSSQL connection URI and a single SQL statement to execute. For read operations (SELECT), use the SQL Server Query node instead. Typical usage: build or provide a connection string, set a reasonable timeout, enter your SQL statement, and connect the outputs to logging or downstream control nodes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database connection URI in MSSQL format. Must include protocol, host, port, database, and authentication details. Example schemes: mssql:// or sqlserver://</td><td style="word-wrap: break-word;">mssql://sa:<password>@db.example.com:1433/mydatabase</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Request timeout in seconds for the execution call to the database service.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL statement to execute. Intended for non-SELECT operations such as INSERT, UPDATE, DELETE, or DDL.</td><td style="word-wrap: break-word;">INSERT INTO users (name, email) VALUES ('John Doe', 'john@example.com')</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the execution outcome (e.g., affected rows).</td><td style="word-wrap: break-word;">Statement executed successfully. Affected rows: 1</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON response from the database service with structured execution details.</td><td style="word-wrap: break-word;">{"affected_rows": 1, "status": "success"}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML table representation of results. For execute operations this is typically empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX data of results. For execute operations this is typically empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF data of results. For execute operations this is typically empty.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Ensure the credentials_path is a valid MSSQL URI (e.g., mssql://user:pass@host:1433/database). Invalid or unsupported schemes will be rejected.
- This node is for non-SELECT statements. Use SQL Server Query for data retrieval to get tabular outputs.
- A properly configured service endpoint for SQL Server must be available; if not set globally, provide service_url via the connection URI query parameters.
- Execution is sent to a remote service; network connectivity and service availability are required.
- Be cautious with dynamic SQL to avoid SQL injection. Provide parameterized or trusted statements when possible.
- The html_table, xlsx_data, and pdf_data outputs are not populated for execute operations and will usually be empty strings.

## Troubleshooting
- Invalid credentials URI: Verify the URI starts with mssql:// or sqlserver:// and includes host, port, and database.
- Connection or timeout errors: Increase the timeout value and ensure the database service endpoint is reachable from your environment.
- Authentication failures: Check username/password, firewall rules, and that the target instance accepts your auth method.
- SQL syntax errors: Validate sql_text by running it directly against the database or testing in a SQL client.
- Insufficient privileges: Ensure the provided user has rights to execute the given statement.
- Service URL not configured: If the workflow environment lacks a configured MSSQL data connector endpoint, include service_url in the connection URI query parameters or update global endpoints.
