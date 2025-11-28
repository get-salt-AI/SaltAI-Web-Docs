# MySQL Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes MySQL data-changing statements (INSERT, UPDATE, DELETE, DDL). It uses stored credentials to connect and returns a human-readable summary along with the raw JSON response.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to perform non-SELECT operations against a MySQL database. Typical workflows include inserting new records, updating existing rows, deleting data, or running schema changes. Place it after a credentials/secret selection step and before any nodes that depend on side effects (e.g., verification queries or downstream processing).

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored MySQL credentials matching the 'mysql' template. Used by the node to authenticate with the database.</td><td style="word-wrap: break-word;">/secrets/mysql/prod</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the execute request to complete before failing.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL statement to execute. Intended for INSERT, UPDATE, DELETE, and DDL operations (non-SELECT).</td><td style="word-wrap: break-word;">INSERT INTO users (name, email) VALUES ('John Doe', 'john@example.com')</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Formatted summary of the execution result (e.g., affected rows, success message).</td><td style="word-wrap: break-word;">MySQL Execute Results: 1 row affected</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON response from the service, which may include affected row counts, status, and any returned metadata.</td><td style="word-wrap: break-word;">{"status":"success","affected_rows":1}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML output placeholder (empty for this node).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">XLSX output placeholder (empty for this node).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF output placeholder (empty for this node).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Non-SELECT only**: Designed for INSERT/UPDATE/DELETE and DDL. Use a query node for SELECT statements.
- **Credentials required**: Ensure the provided credentials path refers to a valid MySQL credential set.
- **Execution effects**: Operations are executed directly on the target database. Validate idempotency when re-running flows.
- **Timeouts**: Long-running statements may need increased timeout values.
- **SQL injection**: Avoid interpolating untrusted input directly into SQL strings.

## Troubleshooting
- **Syntax error**: Verify the SQL statement syntax is valid for MySQL and that referenced tables/columns exist.
- **Permission denied**: Ensure the database user has sufficient privileges for the operation.
- **Connection timeout**: Increase the timeout input or check network connectivity and database availability.
- **No rows affected**: Confirm WHERE clauses and target data match; verify the statement against the current dataset.
- **Invalid credentials path**: Provide a correct credentials_path that matches a valid 'mysql' credential template.
