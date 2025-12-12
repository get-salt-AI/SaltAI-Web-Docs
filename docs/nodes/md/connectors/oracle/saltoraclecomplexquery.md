# Oracle Complex Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes complex Oracle SQL queries (e.g., multi-table JOINs, advanced clauses) against an Oracle database using configured credentials. Sends the query and a declared query_type to the Oracle service and returns formatted results in multiple output modalities.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclecomplexquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to run advanced Oracle queries that go beyond simple SELECTs, such as JOINs, aggregations, and filtered result sets. Typical workflow: provide Oracle credentials, set an appropriate timeout, enter your SQL and specify the query type (e.g., SELECT), then route the outputs (text/JSON/HTML/XLSX/PDF) to downstream nodes for display, parsing, or export.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Path or reference to Oracle credentials used by the service to authenticate the database connection.</td><td style="word-wrap: break-word;"><path-to-oracle-credentials.json></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the database operation before timing out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Oracle SQL query text to execute. Supports complex SQL with JOINs and advanced features.</td><td style="word-wrap: break-word;">SELECT e.employee_id, e.first_name, e.last_name, d.department_name FROM employees e JOIN departments d ON e.department_id = d.department_id WHERE e.salary > 50000</td></tr>
<tr><td style="word-wrap: break-word;">query_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Declares the type of query being executed (e.g., SELECT). Used by the service to handle processing appropriately.</td><td style="word-wrap: break-word;">SELECT</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Human-readable summary or tabular text output of the query results.</td><td style="word-wrap: break-word;">Oracle Complex Query (SELECT): 10 rows returned</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Structured JSON payload of the query results suitable for programmatic consumption.</td><td style="word-wrap: break-word;">{"rows": [{"employee_id": 101, "first_name": "Neena", "last_name": "Kochhar", "department_name": "Executive"}], "row_count": 10}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">HTML representation of the results for rendering in a rich display.</td><td style="word-wrap: break-word;"><table><tr><th>employee_id</th><th>first_name</th>...</tr>...</table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Excel-formatted data for download or export when available.</td><td style="word-wrap: break-word;"><binary-xlsx-data-reference></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">PDF-formatted data for download or export when available.</td><td style="word-wrap: break-word;"><binary-pdf-data-reference></td></tr>
</tbody>
</table>
</div>

## Important Notes
- The node relies on valid Oracle credentials; ensure the provided credentials_path resolves to a correct configuration.
- Long-running or large queries may require increasing the timeout to avoid premature termination.
- Ensure your SQL is valid for Oracle SQL syntax and the specified query_type. Permissions and schema visibility affect accessible tables and columns.
- Parameters like :min_salary in examples require either literal values in the SQL or a service-side mechanism for binding; provide complete executable SQL if bindings are not configured.

## Troubleshooting
- Connection failed or authentication error: Verify credentials_path points to valid Oracle connection details and that network access to the database is allowed.
- Query times out: Increase the timeout value or optimize the SQL (indexes, reduced result set) to improve performance.
- Insufficient privileges or missing objects: Confirm the Oracle user has required permissions and the correct schema/table names are used.
- Invalid SQL or syntax errors: Validate the SQL against Oracle syntax rules and ensure the query_type matches the statement semantics (e.g., SELECT for read queries).
