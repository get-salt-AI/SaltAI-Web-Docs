# Oracle List Schemas

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves a list of all schemas available in an Oracle database using the provided credentials. Returns a readable summary along with a structured JSON payload, and leaves other export formats empty by default.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclelistschemas.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to discover which schemas exist in an Oracle instance before selecting a target schema for subsequent operations (e.g., listing tables, inspecting table info, or building queries). Typical workflow: authenticate with valid Oracle credentials, run this node to enumerate schemas, then feed a chosen schema into downstream nodes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to saved Oracle database credentials. This must point to a valid credential entry configured for Oracle.</td><td style="word-wrap: break-word;">/connections/oracle/prod.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation to complete before timing out.</td><td style="word-wrap: break-word;">60</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the retrieved schemas.</td><td style="word-wrap: break-word;">Oracle Schemas: HR, SYSTEM, APP_DATA</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted result containing the list of schemas and related metadata.</td><td style="word-wrap: break-word;">{"schemas": ["HR", "SYSTEM", "APP_DATA"]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML representation of the result. Empty for default output mode.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Excel data containing the result. Empty for default output mode.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF data containing the result. Empty for default output mode.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials**: Requires a valid Oracle credential configuration. Ensure the user has privileges to view schemas.
- **Performance**: Large environments with many schemas may take longer to respond; adjust the timeout accordingly.
- **Output formats**: By default, this node returns populated text and JSON outputs, while HTML/XLSX/PDF outputs are empty.
- **Ordering**: The order of schemas in the output is not guaranteed unless enforced by the backend.

## Troubleshooting
- **Authentication failed**: Verify the credentials_path points to a valid Oracle credential and that username/password and connection details are correct.
- **Insufficient privileges**: If the result is empty or missing expected schemas, confirm the database user has permission to list schemas.
- **Network or timeout errors**: Increase the timeout value and ensure network access to the Oracle instance is available.
- **Malformed credentials file**: Ensure the credentials file is properly formatted and accessible at the specified path.
