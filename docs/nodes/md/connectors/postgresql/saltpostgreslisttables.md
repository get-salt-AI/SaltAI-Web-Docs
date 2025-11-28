# PostgreSQL List Tables

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lists all tables available in a specified PostgreSQL schema. It uses a database URI to connect through the configured data connector service and returns a human-readable table list along with the raw JSON response.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/postgresql/saltpostgreslisttables.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover or enumerate tables within a specific PostgreSQL schema prior to querying data or building visual queries. Typical workflows include schema exploration, building dropdowns for table selection, and pre-validating table names before composing queries.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database connection URI for PostgreSQL. Must be a valid URI specifying host, port, database, and credentials. Optionally include query parameters (e.g., schema, sslmode).</td><td style="word-wrap: break-word;">postgresql://postgres:<password>@db.example.com:5432/mydb?sslmode=require</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the service request to complete.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">PostgreSQL schema name to list tables from.</td><td style="word-wrap: break-word;">public</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary listing the tables found in the specified schema.</td><td style="word-wrap: break-word;">Tables in schema 'public' (3 tables): - users - orders - products</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string of the raw service response containing the tables.</td><td style="word-wrap: break-word;">{"tables": ["users", "orders", "products"]}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML output (not used by this node; returns empty string).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX export (not used by this node; returns empty string).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF export (not used by this node; returns empty string).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials URI required**: The credentials_path must be a valid PostgreSQL URI (e.g., postgresql://user:<password>@host:port/db).
- **Service configuration**: The node depends on the PostgreSQL data connector service being configured and reachable via the platform’s ENDPOINTS settings.
- **Schema scope**: Only tables within the provided schema are returned. If the schema is empty or incorrect, the result may be an empty list.
- **Output formats**: This node returns only text and JSON; HTML, XLSX, and PDF outputs are intentionally empty strings.
- **Security**: Never embed real credentials in shared workflows. Use secured secrets or environment-managed values.

## Troubleshooting
- **Invalid credentials URI**: If you see an error about the credentials URI, ensure it starts with postgresql:// and includes host, port, database, and proper encoding for special characters in username/password.
- **Service URL not configured**: Errors indicating missing service URL mean the PostgreSQL connector endpoint is not set in ENDPOINTS. Contact an administrator to configure it.
- **Timeouts**: Increase the timeout input if listing large schemas or when the connector service is under load.
- **Empty result**: If no tables are returned, verify the schema name, user permissions on that schema, and that you’re connecting to the intended database.
- **Authentication/SSL issues**: For SSL-required environments, include sslmode in the URI (e.g., sslmode=require). Verify that username/password are correct.
