# Oracle List Tables

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lists all tables available in a specified Oracle schema. If no schema is provided, it lists tables in the current user’s default schema. Returns a readable summary along with structured data suitable for downstream automation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclelisttables.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover available tables in an Oracle database before constructing queries or building data workflows. Typical usage is early in a pipeline to enumerate tables for selection, validation, or UI presentation.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the stored Oracle database credentials to authenticate the request.</td><td style="word-wrap: break-word;">/configs/credentials/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the operation before it times out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">schema_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Oracle schema to list tables from. Leave empty to use the current user schema.</td><td style="word-wrap: break-word;">HR</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the tables found in the target schema.</td><td style="word-wrap: break-word;">Oracle Tables in Schema: HR - EMPLOYEES - DEPARTMENTS - JOBS</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured data containing the list of tables and related metadata, suitable for programmatic use.</td><td style="word-wrap: break-word;">{"tables": ["EMPLOYEES", "DEPARTMENTS", "JOBS"]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML-formatted version of the results (may be empty for this operation).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_file</td><td style="word-wrap: break-word;">FILE</td><td style="word-wrap: break-word;">Spreadsheet export of the results (empty for default output).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_file</td><td style="word-wrap: break-word;">FILE</td><td style="word-wrap: break-word;">PDF export of the results (empty for default output).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Leaving schema_name empty lists tables from the current user’s default schema.
- Valid Oracle credentials must be accessible via credentials_path.
- The operation respects the provided timeout; large schemas or slow connections may require a higher timeout.
- The default output includes text and JSON; other exports (HTML/XLSX/PDF) may be empty for this node.

## Troubleshooting
- Tables not returned: Verify schema_name is correct and that the user has privileges to read metadata in that schema.
- Authentication failure: Check the credentials_path points to a valid Oracle credential configuration.
- Timeouts: Increase the timeout value for large schemas or high-latency environments.
- Empty results with no error: Ensure the target schema exists and contains tables accessible to the authenticated user.
