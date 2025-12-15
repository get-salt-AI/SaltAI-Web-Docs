# Oracle Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes non-SELECT SQL statements (INSERT, UPDATE, DELETE, DDL) against an Oracle database. It uses saved Oracle credentials and a configurable timeout, and returns both a readable summary and the raw JSON response from the service.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracleexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to modify data or schema in an Oracle database (e.g., insert new rows, update existing records, delete rows, or run DDL). Provide the SQL statement in the sql_text input. For data retrieval (SELECT), use SaltOracleQuery instead. Typical workflow: load credentials, set timeout if needed, author your DML/DDL SQL in sql_text, then connect the outputs to logging/validation or follow-up nodes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored Oracle credentials that match the 'oracle' credential template.</td><td style="word-wrap: break-word;">/workspace/credentials/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the operation before timing out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">An Oracle SQL statement to execute (INSERT/UPDATE/DELETE/DDL). The statement should be self-contained; parameter maps are not accepted by this node.</td><td style="word-wrap: break-word;">INSERT INTO employees (employee_id, first_name, last_name) VALUES (1001, 'Ada', 'Lovelace')</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable summary of the execution result.</td><td style="word-wrap: break-word;">Oracle Execute Results: 1 row affected</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON payload returned by the service for programmatic use.</td><td style="word-wrap: break-word;">{"rows_affected": 1, "status": "success"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Non-SELECT only**: This node is intended for INSERT/UPDATE/DELETE or DDL. Use SaltOracleQuery for SELECT queries.
- **Credentials**: Requires a valid Oracle credential set that matches the 'oracle' template.
- **Parameters**: The node does not take a separate parameters map; provide a fully inlined SQL statement.
- **Transactions/Auto-commit**: Execution behavior (commit/rollback) is handled by the backing service; ensure your SQL aligns with your transaction expectations.
- **Timeouts**: Long-running statements may need a higher timeout value to avoid premature failure.
- **Permissions**: Ensure the database user has privileges to perform the requested operation.

## Troubleshooting
- **SQL errors (e.g., ORA-xxxxx)**: Verify your SQL syntax and object names. Ensure the target table/schema exists and the user has required privileges.
- **No rows affected**: Confirm WHERE clauses and data values. If inserting, check for constraint violations and unique keys.
- **Timeouts**: Increase the timeout input for large operations or heavy load. Validate network connectivity to the database service.
- **Authentication/Authorization failures**: Check credentials_path points to valid Oracle credentials matching the expected template and that the account is active.
- **Bind variable issues**: Since parameter maps are not supported, replace placeholders with literal values in sql_text or use a node that supports parameter binding.
