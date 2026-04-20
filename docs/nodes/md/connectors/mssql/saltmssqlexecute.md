# SQL Server Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltMSSQLExecute runs non-SELECT SQL statements (INSERT, UPDATE, DELETE, and other write/DDL operations) against a Microsoft SQL Server database using a configured MSSQL credential profile. It relies on a shared database base node for loading credentials, opening connections, enforcing timeouts, and normalizing the response. This node is specifically intended for write and maintenance actions rather than data retrieval.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqlexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltMSSQLExecute when your workflow needs to modify data or schema in a SQL Server database: adding new records, updating existing rows, deleting data, or applying schema changes such as creating or altering tables and indexes. It typically appears after a configuration or credentials node that supplies a credentials_path and timeout, and before nodes that log, branch, or notify based on whether the operation succeeded. A common pattern is to first use SaltMSSQLQuery to fetch or validate data, then construct a prepared SQL statement in a logic node, and finally send that SQL to SaltMSSQLExecute. Downstream, you might route the JSON result into an audit log node, a monitoring/alert node, or another control node that checks rows_affected to decide the next step. Keep SELECT operations in SaltMSSQLQuery and reserve SaltMSSQLExecute for INSERT/UPDATE/DELETE/DDL to maintain clear separation of responsibilities.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to a stored MSSQL credential configuration that matches the "mssql" credential template. This must include all details required to connect to SQL Server, such as server name, port, database, and authentication settings. It is normally produced automatically by a credential or environment configuration node, not typed directly by end users.</td><td style="word-wrap: break-word;">/runtime/secrets/mssql/customer_prod.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum allowed time in seconds for the SQL operation to run before it is aborted. This protects workflows from hanging on long-running commands. Must be a positive integer; for heavy maintenance operations or large batch updates, set this high enough to accommodate expected runtime.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The T-SQL statement to execute against SQL Server, intended primarily for INSERT, UPDATE, DELETE, MERGE, or DDL statements (CREATE, ALTER, DROP, etc.). The SQL must be syntactically valid for the target SQL Server version. Avoid combining unrelated operations into a single batch unless your database security policy permits it and you understand the transactional implications.</td><td style="word-wrap: break-word;">DELETE FROM audit_logs WHERE created_at < DATEADD(day, -90, SYSDATETIME())</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">A structured JSON object summarizing the outcome of the execution. This typically includes whether the operation succeeded, how many rows were affected, and any informational or error messages from SQL Server or the database wrapper. Downstream nodes can use this to implement conditional logic, retries, or detailed logging.</td><td style="word-wrap: break-word;">{'success': True, 'rows_affected': 1250, 'message': '1250 row(s) affected.', 'error': None}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Bulk modifications and schema changes can be resource-intensive and slow; consider batching large operations and increasing the timeout input to avoid premature termination.
- **Limitations**: The node is not intended to return result sets for SELECT queries; for reading data, use SaltMSSQLQuery instead, which is optimized for retrieval and tabular outputs.
- **Behavior**: Connection management, credential loading, and low-level execution are handled by a shared DatabaseBaseNode; if the credential template "mssql" or the referenced credentials_path is misconfigured, the node will fail before executing any SQL.
- **Behavior**: The sql_text is executed as-is, without automatic parameterization or escaping; building statements from untrusted input can lead to SQL injection or invalid queries, so sanitize and validate all dynamic values before constructing sql_text.

## Troubleshooting
- **Common Error 1**: Authentication errors such as "Login failed for user" usually indicate that credentials_path points to the wrong file or contains invalid login details; confirm that the credential configuration exists, uses the correct server and database, and that the account is active.
- **Common Error 2**: Timeout errors like "Execution timeout expired" mean the operation exceeded the timeout value; optimize the SQL (e.g., add indexes, limit the affected set, split large batches) or increase the timeout input to match the expected runtime.
- **Common Error 3**: SQL syntax errors (e.g., "Incorrect syntax near 'FROM'") arise from malformed sql_text; carefully review the statement, check table and column names, verify quoting and commas, and ensure compatibility with the SQL Server version.
- **Common Error 4**: Permission-related messages such as "The UPDATE permission was denied on the object" indicate that the configured MSSQL user lacks the necessary privileges; adjust database permissions or switch to credentials with the appropriate INSERT/UPDATE/DELETE/DDL rights.
