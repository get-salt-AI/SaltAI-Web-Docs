# Oracle Complex Query

Executes complex Oracle SQL statements, including multi-table joins and advanced clauses. Uses stored Oracle credentials and returns formatted results, typically as readable text plus raw JSON data. Designed to handle more sophisticated queries than the basic query node.

## Usage

Use this node when you need to run advanced Oracle queries (e.g., multiple JOINs, GROUP BY, HAVING, ORDER BY) and get structured results. Commonly placed after a credentials/secret selection step, with downstream nodes consuming either the human-readable text or the JSON payload.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the saved Oracle credentials configuration used for authentication.</td><td style="word-wrap: break-word;">/secrets/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the database operation before it is aborted.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Oracle SQL to execute. Supports complex SELECT queries with JOINs and advanced clauses.</td><td style="word-wrap: break-word;">SELECT e.employee_id, e.first_name, e.last_name, d.department_name FROM employees e JOIN departments d ON e.department_id = d.department_id WHERE e.salary > 50000 ORDER BY d.department_name</td></tr>
<tr><td style="word-wrap: break-word;">query_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A hint indicating the type of query being executed (e.g., SELECT).</td><td style="word-wrap: break-word;">SELECT</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the query result or status.</td><td style="word-wrap: break-word;">Oracle Complex Query (SELECT): 25 rows returned</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw result data and metadata from the Oracle query.</td><td style="word-wrap: break-word;">{"rows": [{"EMPLOYEE_ID": 101, "FIRST_NAME": "Neena", "DEPARTMENT_NAME": "Sales"}], "row_count": 25}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML representation of the results when available; otherwise empty.</td><td style="word-wrap: break-word;"><table><thead><tr><th>EMPLOYEE_ID</th><th>FIRST_NAME</th><th>DEPARTMENT_NAME</th></tr></thead><tbody>...</tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Binary/export handle for an Excel version of the results when available; otherwise empty.</td><td style="word-wrap: break-word;">Not specified</td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">Binary/export handle for a PDF version of the results when available; otherwise empty.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: You must supply a valid Oracle credentials configuration via credentials_path.
- **Query type hint**: query_type defaults to SELECT and should match the kind of SQL you run.
- **Best for SELECT**: Use this node for complex SELECT queries. For INSERT/UPDATE/DELETE, consider using the Oracle Execute node.
- **Parameter placeholders**: If using bind placeholders (e.g., :min_salary), ensure your backend or query includes the necessary literal values or handling, otherwise the database may return an error.
- **Timeouts**: Long-running queries will be aborted if they exceed the specified timeout.

## Troubleshooting
- **Authentication failed**: Verify credentials_path points to a valid Oracle configuration and that the user has necessary permissions.
- **SQL syntax error**: Validate sql_text for Oracle-specific syntax; test the query directly in a SQL client.
- **No results or missing columns**: Confirm the schema/table names and join conditions are correct, and that the user can access those objects.
- **Timeout exceeded**: Increase the timeout input or optimize the query (indexes, filters, reduce joins).
- **Bind variable errors**: Replace placeholders with literals or ensure the runtime environment supports supplying bind values for the query.
