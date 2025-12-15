# MySQL Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes a MySQL SELECT query using configured database credentials and returns the results. The node sends the provided SQL to the MySQL service endpoint and formats the response into a readable text summary and a JSON payload.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqlquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to read data from a MySQL database as part of a workflow. Provide the credentials path, adjust the timeout if needed, and enter a valid MySQL SELECT statement. The resulting text and JSON can feed into downstream data processing, filtering, or visualization nodes. For write operations (INSERT/UPDATE/DELETE), use the dedicated execute node instead.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Path or reference to the MySQL credentials to authenticate the request. Must correspond to the MySQL credential template.</td><td style="word-wrap: break-word;"><path-or-credential-reference></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Maximum time to wait for the query to complete before timing out.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A valid MySQL SELECT query to execute against the target database.</td><td style="word-wrap: break-word;">SELECT * FROM users LIMIT 10</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable summary of the MySQL query results.</td><td style="word-wrap: break-word;">MySQL Query Results: 10 rows returned</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">The raw result data from the query in JSON format.</td><td style="word-wrap: break-word;">{"rows": [{"id": 1, "name": "John"}], "row_count": 10}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **SELECT only**: This node is intended for read-only SELECT queries. For INSERT/UPDATE/DELETE operations, use SaltMySQLExecute.
- **Credentials required**: Ensure the provided credentials path references a valid MySQL credential configuration.
- **Query validity**: The SQL must be valid MySQL syntax and the referenced tables/columns must exist.
- **Timeouts**: Long-running queries may time out; adjust the timeout input or optimize the query.
- **Result size**: Very large result sets can impact performance and may exceed limits; consider adding LIMIT/filters.

## Troubleshooting
- **Authentication failed**: Verify the credentials_path points to a valid MySQL credential and that the database is reachable.
- **SQL syntax error**: Check the sql_text for MySQL syntax issues and correct table/column names.
- **Timeouts or network errors**: Increase the timeout value, ensure network connectivity to the database, and confirm the database is responsive.
- **Empty results**: Confirm the WHERE clause and filters are correct, and that data exists in the target tables.
