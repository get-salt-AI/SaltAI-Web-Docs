# SQL Server List Tables

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node connects to a Microsoft SQL Server instance and returns the list of tables available under a given database schema. It uses shared MSSQL credentials loaded via the database base node and delegates the actual table discovery to a common helper. Use it to quickly discover available tables before constructing queries or building data workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqllisttables.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltMSSQLListTables when you need to know which tables exist in a particular SQL Server schema, either for exploration or to drive dynamic workflows (for example, selecting a table from a list). In a typical pipeline, you first configure MSSQL credentials in Salt's secure credential store, then pass the corresponding `credentials_path` and a `schema` (such as "dbo") into this node. The resulting table list can be fed into nodes like `SaltMSSQLTableInfo` to inspect a specific table, `SaltMSSQLQuery` to build SELECT queries against chosen tables, or custom nodes that iterate over tables for reporting or synchronization. Place this node early in database-centric flows, especially where schemas differ between environments (dev, staging, production). For best results, keep the schema input explicit instead of relying on defaults, reuse a single `credentials_path` across related MSSQL nodes, and adjust the timeout when working with large or busy databases.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Key or path used by Salt to load stored MSSQL connection credentials (server, database, user, etc.) through the shared database base node. This is a reference into Salt's credential store, not raw secrets. The referenced account must have permission to read table metadata in the target database.</td><td style="word-wrap: break-word;">services/mssql/prod_main</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the MSSQL list-tables operation to complete. Increase this value for large schemas or slow networks. If not provided, the default timeout from the underlying database base node is used.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the database schema whose tables you want to list (for example, "dbo" or an application-specific schema). The value must correspond to an existing schema in the target database; otherwise the result may be empty or an error may be returned by the MSSQL service.</td><td style="word-wrap: break-word;">dbo</td></tr>
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
<tr><td style="word-wrap: break-word;">tables</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">JSON-serializable data describing all tables in the specified schema. The exact structure is defined by the shared list-tables helper, but it typically contains an array of table entries (each including at least the table name and schema). Downstream nodes can parse this JSON to select specific tables or iterate over all of them.</td><td style="word-wrap: break-word;">{ "schema": "dbo", "tables": [ { "table_name": "Users", "schema": "dbo" }, { "table_name": "Orders", "schema": "dbo" }, { "table_name": "OrderItems", "schema": "dbo" } ] }</td></tr>
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable summary of the discovered tables, intended for display in the UI or as natural-language context for other nodes that consume plain text.</td><td style="word-wrap: break-word;">Found 3 tables in schema dbo: Users, Orders, OrderItems</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Listing tables in very large schemas or on heavily loaded servers can be slow; if you see repeated timeouts, increase the `timeout` value and consider targeting narrower schemas or running during off-peak hours.
- **Limitations**: The node operates on one schema per invocation. To explore multiple schemas, you must run the node separately for each (for example, once for "dbo" and once for "reporting").
- **Behavior**: The node relies on a shared MSSQL integration in the base database module. If the account referenced by `credentials_path` lacks rights to view catalog metadata, the node may return an empty list or a permission-related error instead of the expected tables.
- **Behavior**: Many setups default the schema to "dbo"; if your application uses custom schemas and you leave the default in place, you might incorrectly assume the database is empty when tables actually live under other schemas.

## Troubleshooting
- **Common Error 1**: "No tables found for schema" even though the database has tables – Verify that the `schema` value exactly matches an existing schema (including case sensitivity rules for your server) and confirm that the user associated with `credentials_path` has permission to read table metadata in that schema.
- **Common Error 2**: Operation times out – Increase the `timeout` input and re-run the node. If the issue persists, check network latency and database load; listing tables in extremely large environments can exceed conservative default timeouts.
- **Common Error 3**: Authentication or permission failures – Errors mentioning login failure, access denied, or insufficient privileges indicate a problem with the stored credential entry. Update the credential referenced by `credentials_path` with the correct server, database, and a user that has catalog-view privileges, then try again.
