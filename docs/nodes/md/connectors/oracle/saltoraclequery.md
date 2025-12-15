# Oracle Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs a read-only SQL SELECT statement against an Oracle database using the configured Oracle credentials. It sends the query to the Oracle service endpoint and returns formatted results as plain text plus a JSON payload, with optional export-friendly formats left empty by default.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclequery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to fetch data from Oracle (SELECT queries). Provide a valid credentials file path and a SELECT statement. Chain its outputs to downstream nodes that parse JSON results or display text summaries. For write operations (INSERT/UPDATE/DELETE), use the Oracle Execute node instead.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the saved Oracle credentials file matching the 'oracle' credential template.</td><td style="word-wrap: break-word;">/workspace/credentials/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the Oracle service to complete the request.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL SELECT statement to execute against Oracle. Use Oracle-specific syntax for limits and joins.</td><td style="word-wrap: break-word;">SELECT * FROM employees WHERE ROWNUM <= 10</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the query result (first rows, row count, etc.).</td><td style="word-wrap: break-word;">Oracle Query Results: 10 rows returned</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON response containing the query result data and metadata.</td><td style="word-wrap: break-word;">{"data": [{"EMPLOYEE_ID": 101, "FIRST_NAME": "Neena"}], "row_count": 1}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of the result (empty by default with the standard formatter).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Excel-formatted binary payload of results (empty by default with the standard formatter).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">PDF-formatted binary payload of results (empty by default with the standard formatter).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is intended for SELECT (read-only) queries. For INSERT/UPDATE/DELETE, use the 'Oracle Execute' node.
- Credentials must match the 'oracle' credential template and be valid for the target database/schema.
- Oracle uses ROWNUM or FETCH FIRST for limiting rows. Ensure your SQL uses Oracle-compatible syntax.
- Large result sets may be subject to service timeouts or size limits; consider filtering or limiting rows.
- The node returns text and JSON by default; HTML/XLSX/PDF outputs are left empty unless a different formatter/export path is used downstream.
- SQL parameters are not automatically bound in this node; provide complete SQL text as executed.

## Troubleshooting
- Query fails with authentication error: Verify credentials_path points to a valid Oracle credential and that the user has necessary permissions.
- Timeouts occur: Increase the timeout input or narrow your query (add filters/limits).
- Syntax errors: Ensure the SQL uses Oracle-specific syntax (e.g., ROWNUM or FETCH FIRST n ROWS ONLY).
- Empty results: Confirm the target schema/table and WHERE conditions; check user permissions on the tables.
- Service connection issues: Ensure the Oracle service is reachable and healthy; retry after verifying network and service status.
