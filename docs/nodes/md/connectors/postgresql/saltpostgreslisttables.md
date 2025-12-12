# PostgreSQL List Tables

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lists all tables available in a specified PostgreSQL schema. It uses provided database credentials to contact the configured PostgreSQL data connector service and returns a readable list as text along with the raw JSON response. Export formats (HTML/XLSX/PDF) are not produced by this node.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgreslisttables.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover what tables exist within a PostgreSQL schema before writing queries or building visual queries. Typical workflow: provide a valid PostgreSQL connection URI, set a schema (e.g., public), run the node to get a table inventory, then pass knowledge of the tables to query or visualization steps.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">PostgreSQL connection URI used to authenticate and reach the database service. Must be a valid database URI.</td><td style="word-wrap: break-word;">postgresql://<username>:<password>@<host>:5432/<database>?schema=public</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the service request to complete.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database schema name to list tables from.</td><td style="word-wrap: break-word;">public</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of tables found in the target schema.</td><td style="word-wrap: break-word;">Tables in schema 'public' (3 tables): - users - orders - products</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded response from the service containing the table list.</td><td style="word-wrap: break-word;">{"tables": ["users", "orders", "products"]}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML export (not produced by this node; will be empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX export (not produced by this node; will be empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF export (not produced by this node; will be empty).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Credentials must be provided as a valid PostgreSQL URI (e.g., starting with postgresql:// or postgres://).
- The node contacts the configured PostgreSQL data connector service; ensure its endpoint is configured in the platform's endpoints settings.
- Only the text and JSON outputs are populated; HTML/XLSX/PDF outputs are intentionally empty for this node.
- The schema must exist and be accessible to the provided user; otherwise, the result may be empty or return an error.
- Timeout controls the underlying service request duration.

## Troubleshooting
- Invalid credentials URI: Ensure the credentials_path is a properly formatted PostgreSQL URI (e.g., postgresql://<username>:<password>@<host>:5432/<database>).
- Service URL not configured: Verify the PostgreSQL data connector endpoint is set in the platform configuration.
- No tables found: Confirm the specified schema exists and that the user has permission to view tables in that schema.
- Request timeout: Increase the timeout value or check network connectivity and service availability.
- Authentication/authorization errors: Verify username/password, database name, and user permissions for the target schema.
