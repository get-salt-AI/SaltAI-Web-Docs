# SQL Server Table Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltMSSQLTableInfo connects to a Microsoft SQL Server using stored credentials and returns metadata about a single table, including its structure and properties. It relies on a shared database base node to manage credentials and timeouts, providing consistent behavior with other SQL Server nodes. Use it to inspect table schemas before building queries, transformations, or validation logic.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqltableinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltMSSQLTableInfo when you need to understand the structure of a particular SQL Server table—such as column names, data types, and key-related information—before writing or generating queries. It is typically placed shortly after configuration or credential nodes that provide `credentials_path` and connection settings, and before nodes like SaltMSSQLQuery or SaltMSSQLExecute that depend on accurate schema assumptions. A common workflow is: (1) configure database access via a credentials node, (2) optionally use SaltMSSQLListTables to discover relevant tables, (3) call SaltMSSQLTableInfo for a chosen table to get its metadata, and (4) feed that metadata to template or logic nodes that construct SQL or validation rules, ultimately passing those to SaltMSSQLQuery. This node integrates naturally with other MSSQL nodes like SaltMSSQLListTables and SaltMSSQLConnectionString to support both exploration and production pipelines.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or identifier for the stored MSSQL credentials that the base database node uses to establish a connection. This should reference a credential entry configured with the 'mssql' template, containing server, database, authentication details, and any required parameters. The value must be valid and accessible in the running environment.</td><td style="word-wrap: break-word;">/configs/credentials/mssql/customer_warehouse.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of seconds to wait for the table information operation to complete. If the metadata query exceeds this duration, the operation is aborted. Choose higher values for slow or distant databases and lower values for fast local environments or interactive use.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the table (without schema prefix) for which to retrieve metadata. The table must exist in the target database under the specified schema. While SQL Server may treat names as case-insensitive depending on collation, it is safest to provide the name as defined in the database.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database schema that contains the target table. In many environments this defaults to 'dbo', but it may be a custom schema such as 'sales', 'hr', or 'analytics'. The combination of schema and table_name must uniquely identify the table.</td><td style="word-wrap: break-word;">dbo</td></tr>
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
<tr><td style="word-wrap: break-word;">table_info</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">A JSON-like structure containing metadata about the specified table as returned by the underlying get_table_info implementation. This typically includes table-level details (name, schema) and a collection of column definitions with names, data types, and nullability flags, and may include additional attributes such as primary keys, identity columns, and indexes. Downstream nodes can use this output to dynamically build SQL, generate forms, or perform schema validation.</td><td style="word-wrap: break-word;">{'table': {'name': 'users', 'schema': 'dbo'}, 'columns': [{'name': 'id', 'data_type': 'int', 'is_nullable': False, 'is_identity': True, 'primary_key': True}, {'name': 'email', 'data_type': 'nvarchar(255)', 'is_nullable': False, 'primary_key': False}, {'name': 'created_at', 'data_type': 'datetime2', 'is_nullable': False, 'primary_key': False}], 'indexes': [{'name': 'PK_users', 'type': 'CLUSTERED', 'columns': ['id']}]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Table metadata lookups are usually fast, but on very large or heavily loaded databases the operation may still be affected by network latency or server load; adjust the timeout accordingly.
- **Limitations**: The node operates on a single table per call; to document an entire schema you must either iterate over table names (possibly retrieved using SaltMSSQLListTables) or design a loop in your workflow.
- **Behavior**: The schema parameter strictly scopes the lookup; if you specify an incorrect schema or omit a non-default schema, the node may fail with a 'table not found' style error even if the table exists elsewhere.
- **Behavior**: The exact shape and richness of the `table_info` JSON depend on the shared DatabaseBaseNode and driver implementation; some advanced metadata (foreign keys, constraints, triggers) may not be present in all environments.

## Troubleshooting
- **Table not found or similar error**: If the node reports that the table does not exist, confirm that `table_name` is spelled correctly and that `schema` matches the actual schema in SQL Server; check with a manual query like `SELECT * FROM [schema].[table]` using another tool.
- **Authentication or connection failures**: Errors mentioning login issues, unreachable server, or invalid connection parameters typically indicate a bad `credentials_path` or misconfigured credentials; verify the path, credential template type ('mssql'), and that the referenced server and database are reachable from the runtime.
- **Timeout exceeded**: When seeing timeout-related errors, increase the `timeout` value and test network connectivity to the database; persistent timeouts may mean the database is under heavy load or there are firewall/VPN issues.
- **Unexpected or missing fields in table_info**: If downstream logic breaks because certain metadata fields are missing or named differently, first inspect the raw `table_info` output to understand its structure, then adjust downstream parsing or mapping logic to that actual structure rather than assuming a fixed schema.
