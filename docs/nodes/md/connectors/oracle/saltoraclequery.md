# Oracle Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltOracleQuery runs read-only SQL SELECT queries on an Oracle database using configured Oracle credentials. It sends the SQL text to a remote Oracle service, observes a timeout, and returns both human-readable text and structured JSON results. The node is intended specifically for SELECT-style data retrieval, not for write or DDL operations.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclequery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to read data from an Oracle database as part of a Salt workflow and pass those results into further analysis, transformation, or reporting steps. It typically follows a step that prepares or chooses a credentials_path, and precedes nodes that parse JSON, aggregate data, visualize tables, or feed data to an LLM. Typical scenarios include querying HR.employees for a report, pulling financial transactions for analytics, or fetching lookup tables to enrich other data sources. Upstream, provide a valid credentials_path for an Oracle connection and a reasonable timeout based on query complexity. Downstream, connect nodes that consume text_output for summaries or logs, or json_output for structured processing. Use SaltOracleExecute for INSERT/UPDATE/DELETE, and related nodes like SaltOracleTableInfo, SaltOracleListTables, and SaltOracleDatabaseInfo to explore schema or metadata. Prefer starting with narrow, filtered SELECT queries (with WHERE and FETCH FIRST clauses) and only then expand scope to avoid performance issues.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or logical reference to the stored Oracle credentials configuration. It must point to a credentials file that follows the oracle credential template expected by the Oracle service, including connection details such as host, port, service or SID, username, and password. The node loads these credentials before sending the query.</td><td style="word-wrap: break-word;">/configs/credentials/oracle/hr-readonly.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds allowed for the remote Oracle query request to complete before it is aborted. Use larger values for complex joins or large datasets and smaller values for quick, interactive queries. This must be a positive integer; values that are too low can cause timeouts even on moderately sized queries.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL SELECT statement to run against the Oracle database. It should be a read-only query; INSERT, UPDATE, DELETE, and DDL statements should be run via SaltOracleExecute instead. Multi-line SQL is supported. Ensure the SQL is valid Oracle syntax and that the user defined in the credentials has privileges on all referenced schemas and tables.</td><td style="word-wrap: break-word;">SELECT employee_id, first_name, last_name, department_id FROM HR.employees WHERE status = 'ACTIVE' AND hire_date >= DATE '2020-01-01' FETCH FIRST 100 ROWS ONLY</td></tr>
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
<tr><td style="word-wrap: break-word;">text_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the Oracle query results, suitable for logging, quick review, or feeding into natural language oriented nodes. Usually includes a title such as Oracle Query Results and a textual representation of columns and a sample of returned rows.</td><td style="word-wrap: break-word;">Oracle Query Results Fetched 10 rows from HR.employees. Columns: EMPLOYEE_ID, FIRST_NAME, LAST_NAME, DEPARTMENT_ID Sample: 100 \| Steven \| King \| 90 101 \| Neena \| Kochhar \| 90 ...</td></tr>
<tr><td style="word-wrap: break-word;">json_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded payload containing the raw query result from the Oracle service. This commonly includes an array of rows and metadata such as column names and row count. Downstream nodes can parse this string as JSON to perform programmatic filtering, aggregation, or visualization.</td><td style="word-wrap: break-word;">{"columns":["EMPLOYEE_ID","FIRST_NAME","LAST_NAME","DEPARTMENT_ID"],"rows":[[100,"Steven","King",90],[101,"Neena","Kochhar",90],[102,"Lex","De Haan",90]],"row_count":3}</td></tr>
<tr><td style="word-wrap: break-word;">html_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of the result set when provided by the underlying base node, often formatted as an HTML table. This can be used in dashboards, email bodies, or web views. It may be an empty string if no HTML representation is generated.</td><td style="word-wrap: break-word;"><h2>Oracle Query Results</h2><table><thead><tr><th>EMPLOYEE_ID</th><th>FIRST_NAME</th><th>LAST_NAME</th><th>DEPARTMENT_ID</th></tr></thead><tbody><tr><td>100</td><td>Steven</td><td>King</td><td>90</td></tr><tr><td>101</td><td>Neena</td><td>Kochhar</td><td>90</td></tr></tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">file_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reference or path to any file artifact produced by the Oracle service for this query, such as an exported spreadsheet or data file. For simple text and JSON-only workflows this may be an empty string.</td><td style="word-wrap: break-word;">/data/exports/hr_employees_active_first100.xlsx</td></tr>
<tr><td style="word-wrap: break-word;">aux_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Auxiliary metadata or diagnostic information about the query execution, typically JSON-encoded. May include execution time, database information, or internal status flags, and can be empty if no extra metadata is provided.</td><td style="word-wrap: break-word;">{"execution_time_ms":342,"database_version":"Oracle Database 19c"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Unbounded SELECT * queries on large tables can be slow and memory intensive; always use WHERE filters and row limits such as FETCH FIRST n ROWS ONLY whenever feasible.
- **Limitations**: This node is intended for read-only SELECT operations; state-changing SQL like INSERT, UPDATE, DELETE, and DDL statements should be executed with SaltOracleExecute instead.
- **Behavior**: Credentials are loaded at runtime from credentials_path using the oracle credential template. If required fields such as host, port, service or SID, username, or password are missing or invalid, the node will fail before executing the query.
- **Behavior**: The SQL is executed by a remote Oracle service endpoint, not locally. Network problems, service downtime, or Oracle-side errors will appear as node errors even if the SQL text itself is valid.
- **Performance**: The timeout value wraps the full remote request. Complex joins, heavy aggregations, or busy database instances may require increasing timeout to avoid premature termination.

## Troubleshooting
- **Common Error 1**: Credential loading failure or authentication errors. Verify that credentials_path is correct, the file exists, and its contents match the oracle template with valid host, port, service or SID, username, and password for the target database.
- **Common Error 2**: Oracle errors such as ORA-00942 indicating that a table or view does not exist. Check schema qualification in sql_text, for example using HR.employees instead of employees, and verify that the user has SELECT privileges on the referenced objects.
- **Common Error 3**: Requests timing out or returning a timeout-related error. Increase the timeout value, optimize the query by adding filters or simplifying joins and sorts, and ensure that the Oracle instance and the Oracle service are reachable with acceptable latency.
- **Common Error 4**: Empty or unexpectedly small result sets. Re-examine WHERE conditions, date filters, and case sensitivity in sql_text, and test the same query directly in an Oracle client to confirm whether the issue lies in query logic or configuration.
