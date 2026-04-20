# MySQL Table Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries a MySQL database (via the configured CData MySQL connector) to return structural information about a given table, such as columns and related metadata. It uses shared database credentials and formats the response into a standard Salt text/JSON output structure. This is useful for inspecting schema details before building queries or data pipelines.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqltableinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltMySQLTableInfo when you need to understand the structure of a specific table in a MySQL database—for example, to see which columns exist, their types, and how to safely construct downstream queries. It typically sits early in a workflow, after credentials are configured with an upstream credential/secret node and often after discovering available databases and tables using nodes like SaltMySQLListDatabases and SaltMySQLListTables. A common pattern is: (1) use SaltMySQLListDatabases to discover databases; (2) use SaltMySQLListTables with a chosen database to see available tables; (3) feed the selected database and table_name into SaltMySQLTableInfo to inspect the table schema; (4) based on the returned structure, construct a query and pass it to SaltMySQLQuery or design insert/update flows for SaltMySQLExecute. Because it returns formatted text plus JSON-structured data, it works well feeding into analysis, visualization, or transformation nodes that accept generic text/JSON/tabular inputs. In practice, use this node whenever you are connecting to a new database, validating assumed schemas, or dynamically generating SQL based on introspected table structure.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filesystem or secret store path indicating where the MySQL credential configuration (matching the mysql credential template) is stored. This must point to valid connection details for the target MySQL instance, such as host, port, username, and authentication method, managed by the CData connector. The format is whatever your Salt environment uses for credential references (for example, a JSON file path or secret key reference).</td><td style="word-wrap: break-word;">/run/secrets/mysql-prod-credentials.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the MySQL table-info request to complete before failing. Use higher values for slow networks or large metadata catalogs. Values should be positive; extremely low timeouts may cause otherwise healthy calls to fail.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the table to retrieve information about within the specified database. This should be the bare table name as understood by MySQL (without database prefix), and it must exist in the database you specify.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The name of the MySQL database (schema) that contains the target table. The node will query this database for metadata about table_name. The default in code is set to "mysql", but in most application scenarios you should set this to your application database.</td><td style="word-wrap: break-word;">customer_app_db</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the table information, suitable for logs, UI display, or feeding into language-model-based nodes. Typically includes the table name, database, and a readable description of columns and their types.</td><td style="word-wrap: break-word;">Table Info: customer_app_db.users Columns: - id (INT, PRIMARY KEY, auto_increment) - email (VARCHAR(255), NOT NULL) - created_at (DATETIME, DEFAULT CURRENT_TIMESTAMP) ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured JSON representation of the table metadata as returned by the underlying CData MySQL /table-info endpoint. Commonly includes an array of column objects (name, data type, nullable flag, default value, key information) and other table-level attributes. This is ideal for programmatic inspection, dynamic query generation, or feeding into downstream transformation and validation nodes.</td><td style="word-wrap: break-word;">{'table': 'users', 'database': 'customer_app_db', 'columns': [{'name': 'id', 'type': 'INT', 'nullable': False, 'primary_key': True, 'auto_increment': True, 'default': None}, {'name': 'email', 'type': 'VARCHAR(255)', 'nullable': False, 'primary_key': False, 'auto_increment': False, 'default': None}, {'name': 'created_at', 'type': 'DATETIME', 'nullable': False, 'primary_key': False, 'auto_increment': False, 'default': 'CURRENT_TIMESTAMP'}]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Table info calls are usually lightweight, but against very large schemas or slow connections, you may need to increase the timeout input to avoid premature failures.
- **Limitations**: The node can only retrieve metadata for tables accessible via the configured MySQL credentials; if the user lacks privileges on a database or table, the call will fail or return limited information.
- **Behavior**: The node always reloads credentials at execution time from credentials_path, so changes to credential files or secret entries take effect without modifying the node configuration.
- **Behavior**: Output JSON structure is determined by the CData MySQL /table-info API; field names and nested structures may vary slightly between connector versions, so avoid hard-coding assumptions without checking actual output.

## Troubleshooting
- **Common Error 1**: "Table not found" or similar message – verify that table_name is spelled correctly, that it is not prefixed with the database name (use just users, not customer_app_db.users), and that the database input points to the correct schema.
- **Common Error 2**: Authentication or connection errors (for example, messages like "Unable to connect to MySQL") – confirm credentials_path points to a valid credential configuration, that the MySQL host is reachable from the Salt execution environment, and that network or firewall rules permit access.
- **Common Error 3**: Timeouts when calling /table-info – increase the timeout value, reduce concurrent database-heavy operations in the same workflow, and ensure the MySQL server is not under heavy load.
- **Common Error 4**: Unexpected or empty JSON output – check that the user specified in the credentials has metadata privileges on the target database and table; if permissions are restricted, broaden privileges or run the node with credentials that can access INFORMATION_SCHEMA for that database.
