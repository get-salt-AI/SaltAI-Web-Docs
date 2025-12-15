# Oracle List Tables

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lists all tables available in a specified Oracle schema using stored Oracle credentials. If no schema name is provided, it lists tables from the current user’s default schema. Returns a readable summary along with structured data for downstream automation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclelisttables.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover what tables exist in an Oracle database before exploring columns, building queries, or creating joins. Typical workflows place this node before a table-info node or a visual/assisted query builder to guide selection.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">CREDENTIALS</td><td style="word-wrap: break-word;">Path or reference to the saved Oracle credential set to authenticate against the database.</td><td style="word-wrap: break-word;"><path-to-oracle-credentials.json></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of seconds to wait for the request before timing out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">schema_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Oracle schema to list tables from. Leave empty to use the current user’s schema.</td><td style="word-wrap: break-word;">HR</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the tables found in the target schema.</td><td style="word-wrap: break-word;">Oracle Tables in Schema: HR - EMPLOYEES - DEPARTMENTS - LOCATIONS</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Structured JSON string of the result, suitable for downstream parsing.</td><td style="word-wrap: break-word;">{"tables": ["EMPLOYEES", "DEPARTMENTS", "LOCATIONS"]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional HTML representation of the result (may be empty depending on formatting).</td><td style="word-wrap: break-word;"><table><tr><th>Table</th></tr><tr><td>EMPLOYEES</td></tr></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional XLSX content or reference for tabular export (may be empty depending on formatting).</td><td style="word-wrap: break-word;"><xlsx-binary-or-reference></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional PDF content or reference for export (may be empty depending on formatting).</td><td style="word-wrap: break-word;"><pdf-binary-or-reference></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Leaving schema_name empty will list tables from the current authenticated user’s default schema.
- Valid Oracle credentials must be provided via credentials_path; insufficient privileges may result in empty or partial results.
- The timeout value controls how long the operation waits for the service to respond.
- Output formats beyond text and JSON may be empty depending on configuration and availability of export formatting.

## Troubleshooting
- No tables returned: Confirm the schema_name is correct and the user has privileges to view tables in that schema.
- Authentication or authorization error: Verify the credentials file at credentials_path and that it matches the required Oracle environment.
- Request timed out: Increase the timeout value or check network connectivity and database responsiveness.
- Unexpected JSON parse issues downstream: Use the json output field rather than parsing the text output.
