# Oracle Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes Oracle Data Manipulation/Definition Language (DML/DDL) statements such as INSERT, UPDATE, DELETE, and other non-SELECT SQL. It connects to an Oracle database using provided credentials, sends the statement to the service, and returns a human-readable summary alongside the raw JSON response.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracleexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to modify data or perform administrative changes in an Oracle database as part of a workflow (e.g., insert new records, update existing rows, or delete data). Typically, place it after a credentials-loading step or in any pipeline where database state changes are required. If you need to retrieve data (SELECT queries), use the corresponding Oracle query node instead.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Path or reference to the Oracle credentials configuration used to authenticate and connect to the database.</td><td style="word-wrap: break-word;">/path/to/oracle_credentials.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Maximum time to wait for the operation to complete before failing.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL statement to execute. Intended for non-SELECT operations such as INSERT, UPDATE, DELETE, or DDL.</td><td style="word-wrap: break-word;">UPDATE employees SET status = 'ACTIVE' WHERE employee_id = 1001</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Human-readable summary of the execution result, suitable for display.</td><td style="word-wrap: break-word;">Oracle Execute Results: 1 row affected.</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Raw JSON result returned by the service, typically including status details and affected row counts.</td><td style="word-wrap: break-word;">{'status': 'success', 'rows_affected': 1}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is designed for non-SELECT statements. For data retrieval, use the Oracle query node.
- Ensure your SQL is valid for Oracle and that the connected user has sufficient privileges to execute it.
- Placeholders such as :id or :first_name require values to be supplied within the SQL or via mechanisms supported by your environment; do not leave bind variables unresolved.
- Execution may return affected row counts or status information rather than data rows.

## Troubleshooting
- SQL error: Verify your statement syntax and confirm it is valid for Oracle. Remove unresolved bind variables or supply concrete values.
- Authentication or connection failure: Check the credentials_path configuration and network/firewall access to the Oracle instance.
- Permission denied: Ensure the database user has privileges for the targeted tables and operations.
- Timeouts: Increase the timeout input for long-running operations or optimize the SQL/indices to reduce execution time.
