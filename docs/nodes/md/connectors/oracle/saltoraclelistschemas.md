# Oracle List Schemas

Retrieves all available schemas from the connected Oracle database using the provided credentials. Returns a human-readable summary and structured JSON, with optional formatted exports.

## Usage

Use this node to discover which schemas exist in your Oracle instance before listing tables or building queries. Place it early in a workflow to validate connectivity and explore the database structure.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the saved Oracle credential profile containing connection details.</td><td style="word-wrap: break-word;">/projects/my-flow/credentials/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the operation before it times out.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Readable summary of the schemas found.</td><td style="word-wrap: break-word;">Oracle Schemas: HR, SALES, SYSTEM, USERS</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured JSON payload containing the list of schemas and related metadata.</td><td style="word-wrap: break-word;">{"schemas": ["HR", "SALES", "SYSTEM", "USERS"], "count": 4}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML-formatted table of schemas for rendering in UI contexts. May be empty if not requested by downstream tools.</td><td style="word-wrap: break-word;"><table><tr><th>Schema</th></tr><tr><td>HR</td></tr></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">FILE</td><td style="word-wrap: break-word;">Excel file export of the schema list when applicable. May be empty if not generated.</td><td style="word-wrap: break-word;"><binary-xlsx-file-reference></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">FILE</td><td style="word-wrap: break-word;">PDF export of the schema list when applicable. May be empty if not generated.</td><td style="word-wrap: break-word;"><binary-pdf-file-reference></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: The node requires a valid Oracle credential profile to connect.
- **Permissions affect results**: The schemas returned depend on the database permissions of the provided user.
- **Timeout behavior**: Long-running or slow connections may require increasing the timeout input.
- **Output variability**: Depending on configuration and downstream usage, only text and JSON may be populated; HTML/XLSX/PDF may be empty.

## Troubleshooting
- **Connection failed**: Verify the credentials file path and contents, network access to the Oracle host, and that the database is reachable.
- **No schemas returned**: Ensure the user has permission to view schemas. Try connecting with a user that has the necessary privileges.
- **Operation timed out**: Increase the timeout input and confirm the database isn't under heavy load.
- **Authentication errors**: Confirm username, password, service name/SID, and connection parameters in the credentials profile.
