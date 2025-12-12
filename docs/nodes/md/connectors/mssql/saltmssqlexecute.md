# SQL Server Execute

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes non-SELECT SQL statements (INSERT, UPDATE, DELETE, DDL) against a Microsoft SQL Server database. It uses a database URI to connect and returns a human-readable summary and a JSON payload with execution details such as affected rows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqlexecute.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to modify data or manage schema in SQL Server. Provide a valid SQL Server connection URI and a DML/DDL statement. Typically used after constructing or retrieving a connection string and before any steps that depend on the data changes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL Server connection URI. Must start with mssql:// or sqlserver:// and include host, port, database, and credentials as needed.</td><td style="word-wrap: break-word;">mssql://sa:<password>@db.example.com:1433/mydatabase</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the operation before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL statement to execute. Intended for INSERT, UPDATE, DELETE, or DDL commands. Supports multiline text.</td><td style="word-wrap: break-word;">INSERT INTO users (name, email) VALUES ('John Doe', 'john@example.com')</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable summary of the execution result, e.g., a success message and the number of affected rows.</td><td style="word-wrap: break-word;">Statement executed successfully. Affected rows: 1</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the raw execution result. Typically includes keys like affected_rows or error details on failure.</td><td style="word-wrap: break-word;">{"affected_rows": 1}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of results when applicable. For execute operations, this is empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX data of tabular results when applicable. For execute operations, this is empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF data of tabular results when applicable. For execute operations, this is empty.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Valid URI required**: credentials_path must be a valid SQL Server URI beginning with mssql:// or sqlserver://.
- **Destructive operations**: This node executes statements that can modify or drop data. Review statements carefully before execution.
- **Authentication**: Include necessary credentials in the URI unless using environment- or platform-specific authentication. Do not hardcode real secrets in shared workflows.
- **Timeouts**: Long-running operations may require increasing the timeout input.
- **Output formats**: For execute operations, only result and json_result are populated; table export fields are empty.

## Troubleshooting
- **Invalid URI error**: Ensure the URI is correctly formatted (e.g., mssql://user:<password>@host:1433/database).
- **Timeouts**: Increase the timeout input or optimize the SQL statement if it runs long.
- **Permission denied**: Verify the user in the URI has sufficient privileges for the operation.
- **SQL syntax errors**: Validate the SQL in sql_text; check the json_result for error messages.
- **No affected rows**: Confirm WHERE clauses and target tables are correct; check json_result to verify execution status.
