# SaltMySQLExecute

Executes non-SELECT SQL statements (INSERT, UPDATE, DELETE, DDL) against a MySQL database using configured credentials. Returns a human-readable summary and the raw JSON response from the underlying service.

## Usage

Use this node to apply changes to a MySQL database or run administrative statements. It typically follows nodes that prepare or validate SQL and precedes nodes that consume the execution result (e.g., logging or branching on success). For data retrieval (SELECT), use the MySQL Query node instead.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored MySQL credentials configuration that the node will use to authenticate.</td><td style="word-wrap: break-word;">/workspace/credentials/mysql.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before failing.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL statement to execute. Intended for INSERT, UPDATE, DELETE, and DDL (e.g., CREATE TABLE) statements.</td><td style="word-wrap: break-word;">INSERT INTO users (name, email) VALUES ('John Doe', 'john@example.com')</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A formatted, human-readable summary of the execution outcome.</td><td style="word-wrap: break-word;">MySQL Execute Results: 1 row affected</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">The raw JSON response from the execution, which may include affected row count, status, and any returned metadata.</td><td style="word-wrap: break-word;">{"affected_rows": 1, "status": "success"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Non-SELECT only**: Use this node for INSERT/UPDATE/DELETE/DDL. For SELECT queries, use the MySQL Query node.
- **Credentials required**: Ensure the credentials file is valid, accessible by the runtime, and grants sufficient privileges for the statement.
- **SQL validation**: The node does not rewrite or validate SQL; syntax and safety are the user's responsibility.
- **Timeouts**: Long-running operations may need increased timeout values to avoid premature failure.
- **Idempotency**: Re-running the same statement can cause duplicate changes. Consider transactions or checks when appropriate.

## Troubleshooting
- **Syntax error in SQL**: Verify the sql_text is valid MySQL syntax and matches your schema (tables, columns, data types).
- **Permission denied**: Ensure the provided credentials have required privileges (e.g., INSERT/UPDATE/DELETE/CREATE).
- **Connection/timeout issues**: Increase the timeout value and verify network access to the MySQL server.
- **Affected rows is zero**: Confirm WHERE clauses are correct and that target rows exist.
- **Invalid credentials path**: Check that credentials_path points to a valid configuration file and is readable by the node.
