# SQL Server Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes non-SELECT SQL commands (INSERT, UPDATE, DELETE, and DDL) against a Microsoft SQL Server database using configured MSSQL credentials. Returns human-readable text and structured outputs suitable for downstream workflow steps.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqlexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to modify data or manage schema in SQL Server during a workflow. Typical examples include inserting new rows, updating existing data, deleting records, or running DDL (e.g., CREATE TABLE). Provide a valid credentials_path, set a timeout, and supply the SQL statement in sql_text.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored MSSQL credentials that follow the 'mssql' credential template.</td><td style="word-wrap: break-word;"><path-to-mssql-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the execution to complete before timing out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL statement to execute (intended for INSERT/UPDATE/DELETE/DDL). Multi-line input is supported.</td><td style="word-wrap: break-word;">INSERT INTO users (name, email) VALUES ('John Doe', 'john@example.com')</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the execution result (e.g., success message or error details).</td><td style="word-wrap: break-word;">Executed successfully. Rows affected: 1</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Structured JSON result for programmatic handling (status, rows affected, errors).</td><td style="word-wrap: break-word;">{"status": "success", "rows_affected": 1}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional HTML representation of the result. Typically empty for execute operations.</td><td style="word-wrap: break-word;"><div><strong>Execution Result:</strong> Rows affected: 1</div></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Optional Excel data. Usually empty for execute operations.</td><td style="word-wrap: break-word;"><bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Optional PDF data. Usually empty for execute operations.</td><td style="word-wrap: break-word;"><bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials template**: Uses the "mssql" credential template; ensure your stored credentials conform to it.
- **Operation scope**: Intended for non-SELECT operations. Use a dedicated query node for SELECT statements.
- **One statement recommended**: Provide a single executable statement. Chain nodes for multi-step operations.
- **Timeout management**: Increase timeout for long-running operations to prevent premature failure.
- **Security**: Do not interpolate untrusted input into SQL to avoid injection vulnerabilities.
- **Permissions**: Ensure the database user has necessary privileges for the operation.

## Troubleshooting
- **SQL syntax errors**: Validate the statement syntax and table/column names.
- **Permission denied**: Verify the MSSQL credentials have required DML/DDL permissions.
- **Operation timed out**: Increase the timeout or optimize the statement/database performance.
- **Invalid credentials_path**: Confirm the path/reference is correct and accessible.
- **Connectivity issues**: Check host, port, network/firewall rules, and server availability.
- **No rows affected**: Review WHERE clauses and data conditions to ensure target rows match.
