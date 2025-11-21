# PostgreSQL Query

Executes a PostgreSQL SELECT query against a configured database connection. Returns a human-readable summary and machine-readable JSON of the result set, with optional export-friendly formats depending on downstream usage. Designed for read-only SQL retrieval operations.

## Usage

Use this node when you need to run a SELECT statement on a PostgreSQL database and pass the results to subsequent steps for analysis, reporting, or export. Typical workflow: provide a credentials file, set an appropriate timeout, author your SQL query, and connect the outputs to viewers, formatters, or file writers.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the PostgreSQL credentials configuration to authenticate the connection.</td><td style="word-wrap: break-word;"><path-to-postgres-credentials.json></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of seconds to wait for the query to complete before aborting.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">sql_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The SQL SELECT statement to execute. Supports multiline input.</td><td style="word-wrap: break-word;">SELECT id, email, created_at FROM users WHERE created_at >= '2024-01-01' ORDER BY created_at DESC LIMIT 100</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Readable summary of the query results, suitable for quick inspection or logging.</td><td style="word-wrap: break-word;">PostgreSQL Query Results (100 rows): ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded data payload of the query results for programmatic use in downstream nodes.</td><td style="word-wrap: break-word;">{"data": [{"id": 1, "email": "user@example.com"}, ...], "row_count": 100}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML table representation of the results (may be empty if not requested/used by downstream consumers).</td><td style="word-wrap: break-word;"><table><thead>...</thead><tbody>...</tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Excel (.xlsx) binary data of the results (may be empty if not requested/used).</td><td style="word-wrap: break-word;"><bytes-of-xlsx-file></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">PDF binary data of the results (may be empty if not requested/used).</td><td style="word-wrap: break-word;"><bytes-of-pdf-file></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Read-only intent**: This node is intended for SELECT queries. Use a dedicated execute node for INSERT/UPDATE/DELETE.
- **Credentials required**: A valid PostgreSQL credentials configuration must be supplied via credentials_path.
- **Timeout behavior**: Long-running queries may be interrupted if they exceed the provided timeout.
- **Result size**: Prefer LIMIT clauses to constrain large result sets to avoid timeouts and excessive memory usage.
- **SQL injection safety**: Avoid interpolating untrusted user input directly into sql_text.
- **Schema qualification**: If multiple schemas exist, qualify tables with schema.table (e.g., public.users) to avoid ambiguity.

## Troubleshooting
- **Authentication or connection failed**: Verify credentials_path, host, port, database, and network access to the PostgreSQL server.
- **Query timed out**: Increase the timeout value or optimize the query with indexes, filters, and LIMIT.
- **SQL syntax error**: Validate sql_text syntax and ensure referenced tables/columns exist.
- **Empty results**: Confirm WHERE conditions and schema/table names; try removing filters or lowering constraints.
- **Permission denied**: Ensure the database user has SELECT privileges on the target schema and tables.
- **Large exports slow or empty**: Use LIMIT and selective columns; ensure downstream consumers expect and handle HTML/XLSX/PDF outputs if used.
