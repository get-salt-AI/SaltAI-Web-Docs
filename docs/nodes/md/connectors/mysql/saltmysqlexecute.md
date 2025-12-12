# MySQL Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes non-SELECT SQL statements (INSERT, UPDATE, DELETE, DDL) against a MySQL database. It uses provided credentials to connect, sends the SQL for execution, and returns a human-readable summary along with a structured JSON payload describing the result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to modify data or schema in a MySQL database as part of a workflow step (e.g., inserting new records, updating fields, deleting rows, or creating tables). Upstream nodes typically supply the credentials path and optional timeout; you provide the SQL statement to execute.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the stored MySQL credentials that this node will use to authenticate.</td><td style="word-wrap: break-word;">/connections/mysql/prod.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the execution to complete before failing.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL statement to execute. Intended for non-SELECT operations such as INSERT, UPDATE, DELETE, or DDL.</td><td style="word-wrap: break-word;">INSERT INTO users (name, email) VALUES ('John Doe', 'john@example.com')</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable summary of the execution result.</td><td style="word-wrap: break-word;">MySQL Execute Results: Affected rows: 1</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured details about the execution result returned by the service.</td><td style="word-wrap: break-word;">{'affected_rows': 1, 'status': 'success'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Ensure your SQL is a non-SELECT statement. For data retrieval, use a MySQL query node instead.
- The node relies on a valid MySQL credential configuration; confirm access rights for the target database and tables.
- Execution results typically include metadata (e.g., affected rows). The exact content may vary by server and statement.
- Use parameterized or pre-validated SQL to mitigate SQL injection risks when incorporating user input.
- Timeouts may occur for long-running operations; adjust the timeout input accordingly.

## Troubleshooting
- Invalid SQL syntax: Validate the statement in a MySQL client and correct any syntax errors.
- Authentication or connection failures: Verify the credentials_path points to valid MySQL credentials and that network access is allowed.
- Insufficient permissions: Ensure the database user has privileges for the target operation (e.g., INSERT/UPDATE/DELETE/DDL).
- Timeout errors: Increase the timeout input or optimize the SQL/indices to reduce execution time.
- No rows affected: Confirm the WHERE clause or target table/values are correct; test the statement directly to verify expected impact.
