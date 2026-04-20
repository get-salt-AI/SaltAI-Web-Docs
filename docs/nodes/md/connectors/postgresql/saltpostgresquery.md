# PostgreSQL Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltPostgresQuery runs SQL SELECT statements against a PostgreSQL database using managed credentials based on the 'postgres' template. It sends the query to a Postgres service endpoint and returns both a human-readable text summary and structured JSON data. The node is focused on read-only queries, making it suitable for analytics, reporting, and feeding downstream nodes with tabular data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltPostgresQuery when you need to read data from PostgreSQL inside a Salt workflow. Typical use cases include fetching recent user activity for personalization, loading product catalogs before recommendation generation, or retrieving transaction records for reporting and analysis. Place this node after a step that provides a valid `credentials_path` for your Postgres connection (for example, a credentials/config node) and any logic that builds or selects the appropriate SQL statement.
In a common pipeline, you might: (1) Define a production Postgres connection, (2) Use a parameter node to capture filters such as date ranges or user segments, (3) Build a SELECT query string using those parameters, then (4) Pass that string into SaltPostgresQuery. The `text` output can be sent directly to LLM or summarization nodes, while the `json` output is ideal for downstream table-processing, filtering, enrichment, or dashboarding nodes. For write operations (INSERT/UPDATE/DELETE), pair this node with SaltPostgresExecute, which is designed for mutations. Always keep queries well-scoped with WHERE clauses and LIMIT to protect performance and avoid unintentionally large result sets.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or identifier to the stored PostgreSQL credentials matching the 'postgres' credential template. This typically references a managed credential object that includes host, port, database name, user, and password or equivalent connection information. It is usually chosen from existing configured connections instead of being typed manually.</td><td style="word-wrap: break-word;">/credentials/datastores/prod_reporting_postgres.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the PostgreSQL service to execute the query and respond. Use higher values for complex queries or large datasets, and lower values for interactive or latency-sensitive tasks. Must be a positive integer; if too small, longer queries may fail with a timeout.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">SQL SELECT query to execute against the PostgreSQL database. The content is sent as-is to the backing Postgres service and must be valid PostgreSQL syntax. It should be strictly read-only (SELECT with optional JOIN, WHERE, GROUP BY, ORDER BY, and LIMIT). Do not include data-changing statements such as INSERT, UPDATE, or DELETE; use SaltPostgresExecute for those.</td><td style="word-wrap: break-word;">SELECT id, email, last_login FROM users WHERE last_login >= NOW() - INTERVAL '30 days' ORDER BY last_login DESC LIMIT 500</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable representation of the query results, usually starting with a label like "PostgreSQL Query Results" followed by a compact listing or summary of returned rows. This format is convenient for quick inspection and for feeding into LLM nodes that expect textual input.</td><td style="word-wrap: break-word;">PostgreSQL Query Results Returned 3 rows from `users`. 1) id=201, email='alex@example.com', last_login='2026-04-18 09:32:10' 2) id=202, email='bianca@example.com', last_login='2026-04-18 08:47:55' 3) id=203, email='carl@example.com', last_login='2026-04-18 08:05:44'</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured JSON payload containing the query results as returned by the Postgres service. Typically includes an array of row objects keyed by column names, plus optional metadata like column lists, row counts, or the executed query. Intended for downstream data transformation, analytics, or integration pipelines.</td><td style="word-wrap: break-word;">{"columns":["id","email","last_login"],"rows":[{"id":201,"email":"alex@example.com","last_login":"2026-04-18T09:32:10Z"},{"id":202,"email":"bianca@example.com","last_login":"2026-04-18T08:47:55Z"}],"rowCount":2,"query":"SELECT id, email, last_login FROM users WHERE last_login >= NOW() - INTERVAL '1 day'"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Execution time scales with query complexity and table size; always add selective WHERE clauses and LIMIT during development to avoid scanning large tables and returning massive result sets.
- **Limitations**: This node is intended for read-only SELECT operations. Statements that modify data (INSERT, UPDATE, DELETE) or change schema should be run via SaltPostgresExecute instead.
- **Behavior**: The node loads credentials from `credentials_path` and calls a Postgres service endpoint (`/query`) under the hood. If credentials are invalid or the service is unreachable, it will fail before executing the SQL.
- **Behavior**: Result formatting is standardized by a shared database base node, so the JSON structure and textual output may not match native psql formatting but will be consistent across other Salt database nodes.

## Troubleshooting
- **Common Error: Authentication failed or connection refused**: Confirm that `credentials_path` points to a valid Postgres credential object. Verify the configured host, port, database name, username, and password, and ensure the database is reachable from the Salt runtime environment.
- **Common Error: Request timed out or exceeded timeout**: Increase the `timeout` value, simplify the SQL query, reduce joins, or add more selective WHERE and LIMIT clauses. Long-running aggregations or unindexed filters are typical causes.
- **Common Error: Syntax error at or near ...**: The `sql_text` is sent directly to PostgreSQL; validate the query in a SQL client connected to the same database to ensure it is syntactically correct and strictly a SELECT. Remove trailing semicolons or unsupported constructs if present.
- **Common Issue: Empty or unexpectedly small result set**: Double-check the WHERE conditions, date ranges, and table or schema names in your query. Run the same SQL directly in a database client to confirm what the database returns and adjust filters accordingly.
