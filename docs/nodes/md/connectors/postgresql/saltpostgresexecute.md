# PostgreSQL Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Runs non-SELECT SQL statements (INSERT, UPDATE, DELETE, DDL) against a PostgreSQL database using provided credentials. Loads credentials from a path, sends the SQL to the PostgreSQL service for execution, and returns a human-readable summary along with a JSON payload.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to modify data or schema in PostgreSQL as part of a workflow (e.g., inserting new records, updating fields, deleting rows, creating tables). Typically, connect it after a credentials/connection provider and before any nodes that depend on the outcome of the write operation.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Path or reference to the stored PostgreSQL credentials to use for authentication.</td><td style="word-wrap: break-word;"><path-to-postgres-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the execution request to complete before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL statement to execute. Intended for INSERT/UPDATE/DELETE or other non-SELECT operations.</td><td style="word-wrap: break-word;">INSERT INTO users (name, email) VALUES ('John Doe', 'john@example.com')</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Human-readable execution summary (e.g., success message, affected rows).</td><td style="word-wrap: break-word;">PostgreSQL Execute Results: 3 rows affected</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Machine-readable JSON response from the execution, which may include status, affected row count, or error details.</td><td style="word-wrap: break-word;">{"status": "success", "row_count": 3}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Do not use this node for SELECT queries; use the PostgreSQL Query node instead.
- Ensure the provided credentials have sufficient privileges for the statements being executed.
- Long-running or large batch operations may require increasing the timeout input.
- The node formats results with a default text summary and a JSON payload; downstream nodes can parse the JSON for programmatic handling.
- Always validate and sanitize dynamic SQL to avoid accidental destructive operations.

## Troubleshooting
- SQL syntax error: Verify the sql_text statement compiles in your database and includes correct schema/table names.
- Permission denied: Confirm the credentials have the required INSERT/UPDATE/DELETE or DDL privileges.
- Timeouts: Increase the timeout input or optimize the query/operation to complete faster.
- Invalid credentials path: Make sure credentials_path points to a valid and accessible credential file/reference.
- No rows affected: Confirm WHERE clauses and target tables; test the statement directly in the database to verify expected impact.
