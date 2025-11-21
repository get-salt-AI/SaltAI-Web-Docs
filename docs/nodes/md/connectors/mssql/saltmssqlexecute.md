# SQL Server Execute

Executes non-query SQL statements (INSERT, UPDATE, DELETE, DDL) against a Microsoft SQL Server using configured credentials. It loads MSSQL credentials, sends the command to the service, and returns an execution result rather than rows of data.

## Usage

Use this node when you need to modify data or schema in SQL Server within a workflow (for example, inserting new records, updating fields, deleting rows, or creating tables). Typically, connect it after a credentials/connection setup step and before any validation or follow-up query nodes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Path or reference to stored MSSQL credentials (uses the 'mssql' credential template).</td><td style="word-wrap: break-word;"><path-to-mssql-credentials.json></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Maximum time to wait for the operation to complete before it fails.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A SQL statement to execute that does not return rows (e.g., INSERT, UPDATE, DELETE, CREATE TABLE).</td><td style="word-wrap: break-word;">INSERT INTO users (name, email) VALUES ('John Doe', 'john@example.com')</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Execution outcome for the SQL command, typically including status and metadata such as affected row count. No tabular result set is returned.</td><td style="word-wrap: break-word;">{"status": "success", "rows_affected": 1}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Only use for non-SELECT statements; to fetch rows use the corresponding SQL Server Query node.
- Ensure the provided credentials have sufficient permissions for the intended operation (e.g., INSERT/UPDATE/DELETE/DDL).
- Avoid constructing SQL with untrusted input to prevent SQL injection; prefer validated or parameterized statements generated upstream.
- Large or long-running operations may require increasing the timeout or optimizing the statement.

## Troubleshooting
- Authentication or authorization failure: Verify credentials_path points to a valid MSSQL credential set and that the user has the required permissions.
- Timeouts: Increase the timeout value or optimize the SQL statement and ensure network connectivity to the database.
- SQL syntax errors: Validate the sql_text syntax against SQL Server dialect and test the statement directly on the target database.
- No rows affected when expected: Confirm WHERE clauses and data values are correct and that triggers or constraints are not preventing changes.
