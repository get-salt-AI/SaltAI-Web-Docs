# Oracle List Tables

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node calls the Oracle service to retrieve a list of tables from a specific Oracle schema. If no schema name is supplied, it lists tables from the current user’s default schema based on the provided credentials. It returns both a human-readable summary and a structured JSON payload suitable for downstream automation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclelisttables.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover or inspect which tables are available in an Oracle database before constructing queries, ETL flows, or other schema-dependent logic. It typically appears early in an Oracle workflow: after you have valid Oracle credentials and before nodes like `SaltOracleTableInfo` (to inspect a specific table) or `SaltOracleQuery` (to select from chosen tables). A common pattern is to first use `SaltOracleListSchemas` to enumerate schemas, then pass a selected schema into `SaltOracleListTables` to list its tables, and finally feed a chosen table into `SaltOracleTableInfo` or `SaltOracleQuery`. Integrate this node with configuration or orchestration nodes that provide `credentials_path` and `timeout`, and parse the `json` output to populate UI dropdowns, validate table names, or generate dynamic SQL. For best results, specify `schema_name` when known to reduce the amount of metadata fetched and improve performance.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or identifier for the Oracle credentials configuration that matches the "oracle" credential template expected by the shared database base logic. It must include server address, port, service/SID, username, password, and any required service options, and be readable at runtime.</td><td style="word-wrap: break-word;">/workspace/credentials/oracle/prod_oracle_credentials.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the Oracle /list-tables request to complete. If the backend or network is slow, or the target schema is large, the operation may approach this limit and fail if it is too low.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">schema_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the Oracle schema whose tables you want to list. If left empty, the node omits this parameter and the backend interprets it as the current user’s default schema. When provided, it should be a valid, accessible schema for the user defined in the credentials.</td><td style="word-wrap: break-word;">HR</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary indicating which schema was queried (a specific schema name or the current user) and that the table list was retrieved. Intended for logs and UI display rather than structured processing.</td><td style="word-wrap: break-word;">Oracle Tables in Schema: HR Retrieved list of tables for schema HR.</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured JSON result from the Oracle service for the /list-tables operation, passed through the default formatter. Typically includes an array of tables and related metadata such as schema and table count; this is the primary output for downstream logic.</td><td style="word-wrap: break-word;">{   "tables": [     {"table_name": "EMPLOYEES", "schema_name": "HR"},     {"table_name": "DEPARTMENTS", "schema_name": "HR"}   ],   "schema": "HR",   "count": 2,   "service": "oracle",   "operation": "list-tables",   "success": true }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Listing tables in large schemas can be slow and may require a higher `timeout`. Providing a specific `schema_name` narrows the scope and can significantly improve response times.
- **Limitations**: Only tables that the connected Oracle user has privileges to view will be included. Tables in other schemas or without appropriate permissions will not appear, even if they exist.
- **Behavior**: If `schema_name` is an empty string, the node sends no schema filter and the backend interprets the request as targeting the current user’s default schema; the human-readable `text` output will describe this as using the current user schema.
- **Behavior**: The exact JSON structure is defined by the Oracle service. While you can expect a collection of tables, downstream consumers should be tolerant of additional fields or minor structural variations over time.

## Troubleshooting
- **Empty or missing tables**: If you expect tables but get an empty list, verify that `schema_name` is correct (including case, as Oracle often stores schema names in uppercase) and that the user in `credentials_path` has privileges on that schema.
- **Connection or authentication errors**: Errors indicating failed connection or invalid credentials usually mean the credentials file is missing, unreadable, or misconfigured. Confirm `credentials_path`, file format, and connection details (host, port, service/SID, username, password).
- **Timeout exceeded**: If the node fails due to timeout, increase the `timeout` value and check database load and network latency. Also reduce the workload by specifying a precise `schema_name` rather than relying on the default current user scope.
- **Unexpected JSON format downstream**: If downstream scripts or nodes fail due to missing or unexpected JSON fields, inspect the raw `json` output from this node and update your parsing logic to match actual keys (for example, consuming the `tables` array) instead of assuming a fixed structure from other services.
