# PostgreSQL Table Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries a PostgreSQL database (through the configured PostgreSQL service) to fetch structural information about a single table in a specified schema. It uses stored PostgreSQL credentials and calls a backend table-info endpoint to obtain metadata. The result is formatted into human-readable text and structured data so you can inspect and use the table definition in downstream steps.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgrestableinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to understand the structure of a specific PostgreSQL table as part of designing, validating, or debugging a data workflow. Common scenarios include exploring unfamiliar databases, confirming column names and data types before writing SQL, and providing schema context to nodes that generate or analyze queries. In a typical pipeline, you first configure and supply a credentials path that matches the 'postgres' credential template, then call this node with a schema and table name. Upstream you might use a connection or configuration node and optionally a PostgreSQL table-listing node to discover valid table names; downstream you often send the text or JSON output into nodes like SaltPostgresQuery (to build accurate SELECT statements), SaltPostgresExecute (for safe DDL/DML against known columns), or text/LLM nodes that convert table metadata into documentation or auto-generated SQL. For best results, always specify the correct schema (not relying only on defaults) and consider caching table metadata for frequently used tables in large or iterative flows.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored PostgreSQL credentials that follow the 'postgres' credential template. The node loads these credentials to authenticate with the PostgreSQL service before requesting table metadata. The path must point to a valid, readable configuration containing the connection details (such as host, port, database, user, and password or equivalent secure parameters).</td><td style="word-wrap: break-word;">/project/config/credentials/postgres/prod_connection.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the table-info request to complete. If the database or network is slow, increase this value; for interactive development you can keep it smaller to fail fast. Must be a positive integer.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the PostgreSQL table whose metadata should be retrieved. Provide the unqualified table name (without schema prefix); the schema is supplied separately. The table must exist in the specified schema and be accessible with the configured credentials.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the database schema where the target table resides. Defaults to "public" in many setups, but should be set explicitly in multi-schema environments. The user identified by the credentials must have permission to read metadata from this schema.</td><td style="word-wrap: break-word;">analytics</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the table information, usually including the fully qualified table name and a concise list or description of its columns and attributes. This is suitable for logging, UI display, or as natural-language context passed into downstream processing or LLM nodes.</td><td style="word-wrap: break-word;">Table Info: analytics.users Columns: - id (integer, primary key) - email (character varying(255), not null, unique) - created_at (timestamp with time zone, not null, default now())</td></tr>
<tr><td style="word-wrap: break-word;">dataframe</td><td style="word-wrap: break-word;">DATAFRAME</td><td style="word-wrap: break-word;">Tabular representation of the table metadata as a dataframe-like structure, with one row per column and fields such as column_name, data_type, is_nullable, and default_value. This is useful for analytic operations, filtering, and any logic that benefits from structured column metadata.</td><td style="word-wrap: break-word;">{'columns': ['column_name', 'data_type', 'is_nullable', 'default_value'], 'rows': [['id', 'integer', 'NO', "nextval('users_id_seq'::regclass)"], ['email', 'character varying(255)', 'NO', None], ['created_at', 'timestamp with time zone', 'NO', 'now()']]}</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Machine-readable JSON object containing the table metadata returned from the PostgreSQL service. Typically includes the fully qualified table name and a list of column objects with fields like name, data_type, nullable, default, and key information. Ideal for downstream nodes that need flexible programmatic access to all metadata fields.</td><td style="word-wrap: break-word;">{'table': 'analytics.users', 'columns': [{'name': 'id', 'data_type': 'integer', 'nullable': False, 'is_primary_key': True, 'default': "nextval('users_id_seq'::regclass)"}, {'name': 'email', 'data_type': 'character varying(255)', 'nullable': False, 'is_primary_key': False, 'default': None, 'unique': True}, {'name': 'created_at', 'data_type': 'timestamp with time zone', 'nullable': False, 'is_primary_key': False, 'default': 'now()'}]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each execution makes a metadata request to the PostgreSQL backend; repeatedly requesting info for the same table in tight loops can add unnecessary latency. Cache or reuse results for frequently referenced tables when possible.
- **Limitations**: This node only retrieves metadata for a single table and schema per run and does not return row-level data or execute arbitrary SQL. Use dedicated query or execution nodes such as SaltPostgresQuery or SaltPostgresExecute for data retrieval or modification.
- **Behavior**: The node relies on an external PostgreSQL integration endpoint (table-info). Connectivity issues, misconfigured endpoints, or insufficient permissions in that backend will result in errors or empty responses, even if the node configuration in your workflow looks correct.
- **Behavior**: Table and schema names are sent as provided; in PostgreSQL setups that use quoted identifiers or unusual casing, you must match the exact names used when the table was created. Mismatches can cause the node to fail to locate the table.

## Troubleshooting
- **Common Error 1**: Empty result or a message indicating the table was not found. Verify that `schema` and `table_name` match exactly what is defined in PostgreSQL, including case and pluralization. In multi-schema environments, ensure you are not accidentally querying the default `public` schema when the table lives elsewhere.
- **Common Error 2**: Timeout or "request timed out" errors. Increase the `timeout` value, confirm network connectivity to the PostgreSQL service, and check that the database is not under heavy load or experiencing slow responses. For remote or busy environments, start with a higher timeout such as 60 seconds.
- **Common Error 3**: Authentication or permission errors, such as inability to connect or access relation metadata. Confirm that `credentials_path` points to the correct credential file, that the credentials are valid, and that the underlying database user has privileges to read metadata for the specified schema and table.
- **Common Error 4**: Returned metadata does not match expectations (missing columns or constraints). Double-check that the credentials target the intended database instance (not a staging or test environment) and that the `schema` input refers to the right logical schema where your production table resides.
