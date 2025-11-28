# Oracle Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs non-SELECT SQL statements (INSERT, UPDATE, DELETE, DDL) against an Oracle database. It uses provided credentials, sends the SQL to the Oracle service, and returns a human-readable summary plus the raw JSON result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracleexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to modify data or schema in an Oracle database as part of a workflow (e.g., inserting records, updating fields, deleting rows, creating/dropping tables). Typically placed after a credentials-loading step, it executes the given SQL text within the configured timeout and returns execution results for logging or branching.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored Oracle credentials configuration to authenticate the request.</td><td style="word-wrap: break-word;">/path/to/credentials/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before timing out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL statement to execute. Intended for INSERT/UPDATE/DELETE or DDL statements.</td><td style="word-wrap: break-word;">UPDATE employees SET salary = salary * 1.05 WHERE department_id = 10</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Readable summary of the execution (e.g., operation type and outcome).</td><td style="word-wrap: break-word;">Oracle Execute Results: 25 rows affected.</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON payload returned by the Oracle service with details about the execution.</td><td style="word-wrap: break-word;">{"status":"success","rows_affected":25}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is designed for executing non-SELECT statements; for SELECT queries, use the Oracle Query node.
- Credentials must be valid and grant sufficient permissions for the SQL being executed.
- Bind variables are not supported as separate inputs; provide fully formed SQL with literal values or ensure the service can handle your SQL as provided.
- Execution behavior (e.g., transaction handling, autocommit) follows the backing Oracle service configuration.
- Large or long-running statements may require increasing the timeout to avoid premature failure.

## Troubleshooting
- SQL errors (e.g., ORA-00900, ORA-00933): Verify your SQL syntax and ensure it matches Oracle SQL standards.
- Permission denied (e.g., ORA-01031): Ensure the credentialed user has the necessary privileges for the target tables or DDL.
- Connection/timeout issues: Check network access to the Oracle service and increase the timeout input if the operation is expected to run long.
- No rows affected when expected: Confirm WHERE clause conditions and that the target records exist.
- Service returned unexpected JSON: Inspect the json output for error fields and message details to guide remediation.
