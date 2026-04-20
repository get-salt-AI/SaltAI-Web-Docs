# PostgreSQL Visual Query Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node constructs complex PostgreSQL SELECT queries from structured inputs instead of raw SQL text. You specify the main table, JOINs, filters, grouping, ordering, limits, and offsets, and the node builds a query string and executes it using a configured PostgreSQL service. It returns results as formatted text plus JSON, or as HTML, Excel, PDF, or all supported formats depending on the selected output mode.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgresvisualquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want a configuration-driven, reusable PostgreSQL query, especially for reporting and analytics scenarios where non-SQL users need control over joins and filters. Place it after a step that supplies the PostgreSQL `credentials_path` and `timeout`, such as a credentials loader or configuration node, and before export, visualization, or notification nodes that consume its text, JSON, HTML, XLSX, or PDF outputs. A common pipeline is: load credentials → explore schema with `PostgreSQL List Tables` or `PostgreSQL Table Info` → design relationships and requirements with `PostgreSQL Query Builder Helper` → implement the final query via `PostgreSQL Visual Query Builder` → send the outputs to reporting (e.g., daily user-order report) or archival nodes. Prefer this node over simple SQL text nodes when you want to standardize how JOINs and conditions are expressed (through JSON configuration) and when you need built-in multi-format exports.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or handle to the stored PostgreSQL credentials using the `postgres` credential template. This credential must have permission to read from all referenced schemas and tables.</td><td style="word-wrap: break-word;">/workspace/credentials/prod/postgres.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of seconds to wait for the PostgreSQL request before failing. Higher values are suitable for heavy aggregations; lower values are better for interactive usage.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">main_table</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the primary table used in the FROM clause, without schema qualification. All JOINs and conditions are built around this table.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">main_table_schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema name of the main table, such as `public` or your app-specific schema. If left empty, the query references the table without a schema prefix.</td><td style="word-wrap: break-word;">public</td></tr>
<tr><td style="word-wrap: break-word;">selected_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Columns for the SELECT clause, as a comma-separated list, or `*` to select all columns. You may include fully qualified names, expressions, and aliases, such as `users.id, users.email, SUM(orders.total_amount) AS total_spent`.</td><td style="word-wrap: break-word;">users.id, users.email, orders.total_amount, orders.created_at</td></tr>
<tr><td style="word-wrap: break-word;">join_config</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array of JOIN definitions. Each entry must be an object with: `type` (one of INNER, LEFT, RIGHT, FULL, CROSS), `table` (the table to join), optional `schema` (defaults to `public` if omitted), and `on` (the SQL join condition). The entire string must be valid JSON that parses to a list.</td><td style="word-wrap: break-word;">[{"type": "LEFT", "table": "orders", "schema": "public", "on": "users.id = orders.user_id"}, {"type": "INNER", "table": "payments", "schema": "billing", "on": "orders.id = payments.order_id"}]</td></tr>
<tr><td style="word-wrap: break-word;">where_conditions</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional filter expression for the WHERE clause, without the `WHERE` keyword. This string is concatenated directly into the SQL, so it must be valid PostgreSQL boolean logic.</td><td style="word-wrap: break-word;">users.status = 'active' AND orders.created_at >= '2024-01-01'</td></tr>
<tr><td style="word-wrap: break-word;">group_by_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional GROUP BY columns as a comma-separated list, without including the `GROUP BY` keyword. Leave empty when you are not using aggregate functions.</td><td style="word-wrap: break-word;">users.id, users.country</td></tr>
<tr><td style="word-wrap: break-word;">having_conditions</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional HAVING clause conditions applied after grouping, without the `HAVING` keyword. Only relevant if `group_by_columns` is specified.</td><td style="word-wrap: break-word;">COUNT(orders.id) > 5</td></tr>
<tr><td style="word-wrap: break-word;">order_by_columns</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional ORDER BY specification without the `ORDER BY` keyword. Can include multiple columns and sort directions such as `ASC` or `DESC`.</td><td style="word-wrap: break-word;">users.created_at DESC, users.id ASC</td></tr>
<tr><td style="word-wrap: break-word;">limit_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">LIMIT value to cap the maximum number of rows returned. Must be between 1 and 10000, ensuring that accidental full-table reads are constrained.</td><td style="word-wrap: break-word;">500</td></tr>
<tr><td style="word-wrap: break-word;">offset_count</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">OFFSET value for pagination, specifying how many rows to skip before starting to return results. Must be between 0 and 10000.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">ENUM[text, html, xlsx, pdf, all]</td><td style="word-wrap: break-word;">Specifies how the results are returned. `text` provides a formatted text summary and JSON; `html` returns an HTML table; `xlsx` returns an Excel workbook as bytes; `pdf` returns a PDF as bytes; `all` returns all supported exports via the node’s default export behavior.</td><td style="word-wrap: break-word;">html</td></tr>
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
<tr><td style="word-wrap: break-word;">text_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Formatted, human-readable representation of the query results when `output_format` is `text` or `all`. This may be empty when only non-text formats are requested.</td><td style="word-wrap: break-word;">Visual Query Results (2 rows): ================================================== Row 1:   id: 42   email: alice@example.com   total_amount: 199.99   created_at: 2024-03-15T10:24:00  Row 2:   id: 43   email: bob@example.org   total_amount: 49.50   created_at: 2024-03-15T11:02:17</td></tr>
<tr><td style="word-wrap: break-word;">json_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded structure containing the raw query results. Typically this is an object with a `data` array of rows and optional metadata such as `row_count`.</td><td style="word-wrap: break-word;">{"data": [{"id": 42, "email": "alice@example.com", "total_amount": 199.99, "created_at": "2024-03-15T10:24:00"}, {"id": 43, "email": "bob@example.org", "total_amount": 49.5, "created_at": "2024-03-15T11:02:17"}], "row_count": 2}</td></tr>
<tr><td style="word-wrap: break-word;">html_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML table markup representing the query results when `output_format` is `html` or `all`. It will be an empty string if HTML output is not generated.</td><td style="word-wrap: break-word;"><h3>Visual Query: public.users</h3><table><thead><tr><th>id</th><th>email</th><th>total_amount</th><th>created_at</th></tr></thead><tbody><tr><td>42</td><td>alice@example.com</td><td>199.99</td><td>2024-03-15T10:24:00</td></tr><tr><td>43</td><td>bob@example.org</td><td>49.50</td><td>2024-03-15T11:02:17</td></tr></tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_output</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary Excel (.xlsx) workbook of the query results when `output_format` is `xlsx` or `all`. This can be written directly to a file or sent to downstream email/export nodes.</td><td style="word-wrap: break-word;">UEsDBBQABgAIAAAAIQAAAAAAAAAAAAAAAAAJAAAAd29ya2Jvb2sueGxzeO0</td></tr>
<tr><td style="word-wrap: break-word;">pdf_output</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF document representing the tabular query results when `output_format` is `pdf` or `all`. This is suitable for fixed-format reports and attachments.</td><td style="word-wrap: break-word;">JVBERi0xLjQgJeLjz9MKNCAwIG9iago8PC9UeXBlIC9QYWdlL1BhcmVudCAxIDAgUi9Db250ZW50cyA1IDAgUj4+</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node enforces a LIMIT and optional OFFSET to control result size; high `limit_count` values on large joined tables can still be slow or memory-intensive.
- **Limitations**: `join_config` must be valid JSON that parses to a list and each entry must contain `type`, `table`, and `on`. Missing fields or wrong types will cause early validation errors before SQL execution.
- **Behavior**: JOIN types are validated and normalized to uppercase, and must be one of INNER, LEFT, RIGHT, FULL, or CROSS. If a join entry does not specify `schema`, it automatically defaults to `public`.
- **Behavior**: Depending on the chosen `output_format`, only some outputs are populated (for example, `html_output` is only filled when `output_format` is `html` or `all`), while others may be empty strings or empty binary blobs.

## Troubleshooting
- **Common Error 1**: `Invalid JSON in join configuration` – The `join_config` string cannot be parsed. Ensure you provide proper JSON with double quotes, no trailing commas, and a top-level array, such as `[{"type": "INNER", "table": "orders", "on": "users.id = orders.user_id"}]`.
- **Common Error 2**: `Join X missing required field: type` or `table` or `on` – One of the join objects is missing a required key. Check each element in `join_config` to confirm it is an object containing `type`, `table`, and `on` keys.
- **Common Error 3**: `Invalid join type: ... Must be one of: INNER, LEFT, RIGHT, FULL, CROSS` – A join definition has an unsupported `type`. Update `type` to one of the allowed values; case is not important but spelling must be exact.
- **Common Error 4**: `Visual query execution failed: ... syntax error at or near` – The assembled SQL contains syntax errors, often from invalid WHERE, GROUP BY, HAVING, or ORDER BY strings. Simplify these clauses and test the generated SQL directly in a SQL client to identify and fix the issue.
