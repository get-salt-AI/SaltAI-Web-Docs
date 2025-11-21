# Oracle Query

Executes a read-only SQL SELECT statement against an Oracle database using stored credentials. Returns a human-readable text summary and a structured JSON payload by default. Designed to run ad-hoc queries; write operations are not performed by this node.

## Usage

Use this node when you need to fetch data from an Oracle database using a SELECT query. Typical workflow: provide a credentials file path, set an optional timeout, enter your SQL SELECT, and connect the outputs to downstream nodes that consume text summaries or JSON result sets. For INSERT/UPDATE/DELETE, use the Oracle Execute node instead.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Path or reference to the saved Oracle credentials to authenticate the request.</td><td style="word-wrap: break-word;">/workspace/credentials/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Request timeout in seconds for the database operation.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL SELECT query to execute against the Oracle database.</td><td style="word-wrap: break-word;">SELECT * FROM employees WHERE rownum <= 10</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Readable summary or tabular preview of the query results.</td><td style="word-wrap: break-word;">Oracle Query Results: 10 rows fetched from employees</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Structured JSON containing the query results and metadata.</td><td style="word-wrap: break-word;">{"rows": [{"EMPLOYEE_ID": 100, "FIRST_NAME": "Steven"}], "row_count": 10}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">HTML representation of results (empty for default formatting).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Binary/encoded Excel worksheet of results (empty for default formatting).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Binary/encoded PDF table of results (empty for default formatting).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Only SELECT queries are intended for this node; use the Oracle Execute node for INSERT/UPDATE/DELETE.
- Results are returned with text and JSON populated by default; HTML/XLSX/PDF outputs are not populated unless using nodes that explicitly export those formats.
- Ensure the provided credentials have appropriate read permissions on the target schema/tables.
- Long-running queries may hit the timeout; optimize SQL or increase the timeout as needed.

## Troubleshooting
- Query fails with an ORA- error: Validate the SQL syntax and referenced schema/table names; ensure the user has necessary privileges.
- No results returned: Confirm WHERE conditions and schema context; try running the query directly in a SQL client to verify.
- Authentication or connection errors: Check the credentials_path content, host/port/service name in the stored credentials, and network access.
- Timeouts: Simplify or index your query, or increase the timeout input to accommodate larger scans or complex joins.
