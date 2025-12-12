# Oracle Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes a read-only SQL SELECT query against an Oracle database using configured credentials. Loads credentials from a provided path, sends the query to the Oracle service, and returns results formatted as text and JSON (with optional HTML/XLSX/PDF placeholders).

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclequery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to retrieve data from Oracle tables with a SELECT statement. Provide a valid credentials file path and your SQL query; the node will run the query and return results suitable for inspection or passing to downstream nodes for display or export. For INSERT/UPDATE/DELETE, use the Oracle Execute node instead.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filesystem path to the saved Oracle credentials configuration used by the service.</td><td style="word-wrap: break-word;"><path-to-oracle-credentials.json></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the query execution before timing out.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Oracle SQL SELECT statement to execute. Supports multiline input.</td><td style="word-wrap: break-word;">SELECT * FROM employees WHERE ROWNUM <= 10</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary or table-like text of the query results.</td><td style="word-wrap: break-word;">Oracle Query Results: 10 rows returned</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured JSON payload of the query results and metadata.</td><td style="word-wrap: break-word;">{"rows": [{"EMPLOYEE_ID": 100, "FIRST_NAME": "Steven"}], "rowCount": 10}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of the result set (may be empty when default formatting is used).</td><td style="word-wrap: break-word;"><table><tr><th>EMPLOYEE_ID</th><th>FIRST_NAME</th></tr><tr><td>100</td><td>Steven</td></tr></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary XLSX export of the result set (may be empty when default formatting is used).</td><td style="word-wrap: break-word;"><binary-xlsx-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF export of the result set (may be empty when default formatting is used).</td><td style="word-wrap: break-word;"><binary-pdf-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Only SELECT queries are intended for this node. Use Oracle Execute for INSERT/UPDATE/DELETE.
- Oracle-specific SQL features and syntax apply (e.g., ROWNUM or FETCH FIRST for limiting rows).
- The node relies on a valid Oracle credentials configuration; ensure the credentials file is present and correctly formatted.
- Timeout applies to the full request; long-running queries may need a higher timeout.
- Default formatting typically populates text and JSON; HTML/XLSX/PDF outputs may be empty unless the service provides them.

## Troubleshooting
- Query fails with authentication or connection errors: Verify the credentials_path points to a valid Oracle credentials file and that network access is allowed.
- Syntax error from database: Check that sql_text is valid Oracle SQL (including correct quoting and Oracle functions).
- Timeouts on large queries: Increase the timeout input or optimize the query (indexes, filters, smaller result sets).
- Empty results: Confirm the WHERE clause and schema/table names are correct and that the user has access to the targeted objects.
- Unexpected empty HTML/XLSX/PDF outputs: This is expected with default formatting; use nodes or flows that export results if you require these formats.
