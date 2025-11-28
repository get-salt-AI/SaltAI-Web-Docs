# Oracle Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes a read-only SQL SELECT query against an Oracle database through the Salt data-connector service. It loads connection credentials from a database URI, calls the Oracle service endpoint, and returns both a human-readable summary and raw JSON results.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclequery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to fetch data from an Oracle database with a SELECT statement. Provide a valid Oracle connection URI and your SQL text. Typical workflow: set credentials_path, optionally adjust timeout, enter your SELECT query, then consume either the formatted text result for quick inspection or the JSON output for downstream processing. For write operations (INSERT/UPDATE/DELETE), use SaltOracleExecute instead.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Oracle database connection URI. Must use the oracle:// scheme. The path component determines SID or service name: if it contains a dot it is treated as a service_name; otherwise it is treated as a SID.</td><td style="word-wrap: break-word;">oracle://scott:<password>@db-host.example.com:1521/ORCL</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Request timeout in seconds for the query execution against the data-connector service.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL SELECT statement to execute against the Oracle database.</td><td style="word-wrap: break-word;">SELECT employee_id, first_name, last_name FROM employees WHERE ROWNUM <= 10</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the query results, including row count and row-by-row key-value display.</td><td style="word-wrap: break-word;">Query Results (10 rows): employee_id: 100 \| first_name: Steven \| last_name: King employee_id: 101 \| first_name: Neena \| last_name: Kochhar</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON response string containing the data returned by the service, suitable for downstream parsing.</td><td style="word-wrap: break-word;">{"data":[{"EMPLOYEE_ID":100,"FIRST_NAME":"Steven","LAST_NAME":"King"}],"row_count":1}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML table representation of the results. For this node’s default output mode, this is returned as an empty string.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX content of the results. For this node’s default output mode, this is returned as an empty string.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF content of the results. For this node’s default output mode, this is returned as an empty string.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Read-only operation**: This node is intended for SELECT queries. Use SaltOracleExecute for INSERT/UPDATE/DELETE.
- **Credentials URI required**: The credentials_path must be a valid Oracle URI (oracle://...). If the path after the host contains a dot, it is interpreted as service_name; otherwise as SID.
- **Service dependency**: The node communicates with the configured Oracle data-connector service endpoint. Ensure the oracle_data_connector endpoint is configured in your environment.
- **Output formats**: Only the formatted text and JSON outputs are populated by default. HTML, XLSX, and PDF outputs are empty strings in this node.
- **Timeouts**: Long-running queries may require increasing the timeout value.
- **Security**: Do not paste real passwords into shared workflows. Use secure secret management where possible.

## Troubleshooting
- **Invalid credentials URI**: If you see an error like "Invalid credentials URI" or "Credentials URI is required", verify the URI format: oracle://<user>:<password>@<host>:<port>/<SID_or_service>.
- **Service URL not configured**: Errors about missing service URL indicate the oracle_data_connector endpoint is not set in environment configuration. Configure it before running.
- **Request failed or timed out**: If requests fail or timeout, check network access to the data-connector service, increase the timeout, and confirm the Oracle instance is reachable.
- **Unsupported SQL**: If the query returns errors, ensure you are using a valid SELECT statement supported by Oracle and that referenced schemas/tables are accessible to the provided user.
- **No data returned**: If result shows "No data returned", verify your WHERE clause, user permissions, and that the target table contains matching rows.
- **SID vs Service Name**: If connection fails, confirm that the database path in the URI correctly represents SID or service_name; e.g., oracle://user:pass@host:1521/ORCL (SID) vs oracle://user:pass@host:1521/orcl.example.com (service name).
