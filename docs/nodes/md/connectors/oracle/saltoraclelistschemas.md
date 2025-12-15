# Oracle List Schemas

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lists all available schemas in the connected Oracle database. It uses provided Oracle credentials to connect and returns a human-readable summary along with structured JSON data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclelistschemas.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node early in a database exploration workflow to discover which schemas exist before listing tables or querying specific objects. Provide a valid Oracle credential set and an appropriate timeout. The output includes both text and JSON so you can branch it to visualization or downstream logic.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the saved Oracle credentials to use for the connection.</td><td style="word-wrap: break-word;"><path-to-oracle-credentials.json></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before timing out.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A formatted, human-readable summary of the Oracle schemas.</td><td style="word-wrap: break-word;">Oracle Schemas: HR, SYSTEM, SYS, APP_DATA</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured data containing the list of schemas and any additional metadata.</td><td style="word-wrap: break-word;">{"schemas": ["HR", "SYSTEM", "SYS", "APP_DATA"]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML representation (empty for default output of this node).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Excel file content (empty for default output of this node).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF content (empty for default output of this node).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials**: Requires a valid Oracle credential configuration. Ensure the credential template matches the Oracle service.
- **Permissions**: The connected user must have privileges to view schemas; results may be limited by database permissions.
- **Timeouts**: Large environments or slow connections may require increasing the timeout value.
- **Outputs**: By default, this node returns text and JSON. HTML/XLSX/PDF outputs are not populated for this operation.

## Troubleshooting
- **Authentication failed**: Verify the credentials file path and contents, and ensure host/port/service name are correct.
- **Timeout or network errors**: Increase the timeout value and confirm network access to the Oracle server.
- **Empty or partial schema list**: Check user permissions in Oracle; lack of privileges can hide schemas.
- **Unexpected output format**: Ensure downstream nodes handle text/JSON; this node does not generate HTML/XLSX/PDF by default.
