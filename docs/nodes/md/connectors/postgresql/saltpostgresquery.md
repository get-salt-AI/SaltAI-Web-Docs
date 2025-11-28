# PostgreSQL Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes a PostgreSQL SELECT query through Salt’s CData-backed database service. It uses provided credentials to connect to a PostgreSQL database, sends the SQL to the service, and returns a formatted summary along with the raw results. This node is read-focused and intended for retrieving data, not performing INSERT/UPDATE/DELETE operations.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to fetch records from a PostgreSQL database as part of a workflow (e.g., pulling rows for analysis or feeding downstream processing). Provide a valid credentials file for the PostgreSQL connection and a SELECT statement. The node will call the backend service and return a readable text summary and structured results suitable for further steps.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Path to the saved PostgreSQL credential configuration used by the service to authenticate and connect.</td><td style="word-wrap: break-word;">/workspace/credentials/postgres.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the query request before timing out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL SELECT query to execute against PostgreSQL.</td><td style="word-wrap: break-word;">SELECT id, email FROM users WHERE created_at >= '2024-01-01' LIMIT 100</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">A human-readable summary of the query results suitable for display.</td><td style="word-wrap: break-word;">PostgreSQL Query Results: 100 rows returned.</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Raw results from the service as structured data for downstream processing.</td><td style="word-wrap: break-word;">{"rows": [{"id": 1, "email": "user@example.com"}], "row_count": 1}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Query type**: Designed for SELECT queries only. Use a separate execute node for INSERT/UPDATE/DELETE.
- **Credentials required**: You must supply a valid PostgreSQL credential file that the service recognizes.
- **Timeouts and large results**: Complex or large queries may require increased timeouts or result limiting.
- **Service-backed**: The node routes requests to a backend service endpoint ("/query"); connectivity and service availability affect results.
- **Result formatting**: Returns both a readable summary and structured data to support human review and automated use.

## Troubleshooting
- **Invalid SQL or syntax errors**: Verify the SQL is a valid SELECT statement and test it directly in your database; ensure references to schemas/tables are correct.
- **Authentication or connection failures**: Check the credentials_path points to a valid PostgreSQL configuration and that network/firewall settings allow service access.
- **Timeouts**: Increase the timeout input or optimize the query with indexes, filters, or LIMIT.
- **Empty results**: Confirm the query conditions and that the connected database/schema contains the expected data.
- **Permission errors**: Ensure the configured database user has SELECT privileges on the target tables/views.
