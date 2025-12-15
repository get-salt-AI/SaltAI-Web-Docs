# SQL Server List Tables

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lists all table names in a specified SQL Server schema using saved MSSQL credentials. Loads credentials, calls the backend list-tables operation, and returns a human-readable summary along with structured results.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqllisttables.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover or validate the tables available in a given SQL Server schema (e.g., before building queries or selecting a table for downstream operations). Typical workflow: provide your MSSQL credentials, set the schema (dbo by default), optionally adjust timeout, and connect the outputs to nodes that consume JSON or tabular data.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">CREDENTIALS</td><td style="word-wrap: break-word;">Reference to saved MSSQL credentials matching the 'mssql' template. These credentials are used to authenticate with the database service.</td><td style="word-wrap: break-word;"><path-or-credential-reference></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before timing out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the database schema to list tables from.</td><td style="word-wrap: break-word;">dbo</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Human-readable summary of the operation and the schema’s tables.</td><td style="word-wrap: break-word;">Tables in Schema: dbo</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Structured results containing the list of tables.</td><td style="word-wrap: break-word;">{"tables": ["users", "orders", "products"]}</td></tr>
<tr><td style="word-wrap: break-word;">table</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Tabular representation of the listed tables (typically one row per table).</td><td style="word-wrap: break-word;">Not specified</td></tr>
<tr><td style="word-wrap: break-word;">extra_1</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Reserved/auxiliary output for additional data, if any.</td><td style="word-wrap: break-word;">Not specified</td></tr>
<tr><td style="word-wrap: break-word;">extra_2</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Reserved/auxiliary output for additional data, if any.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The node requires valid MSSQL credentials saved under the 'mssql' credential template.
- Schema is required; if your environment uses non-default schemas, set the appropriate schema name.
- Timeout controls the maximum wait for the backend service; long-running metadata operations may require higher values.
- Outputs include both a readable summary and structured results suitable for downstream parsing.

## Troubleshooting
- If you receive authentication errors, verify the credentials referenced by credentials_path and ensure the user has access to the target database/schema.
- If the result is empty but no error occurs, confirm the schema name is correct and that it contains tables.
- On timeout errors, increase the timeout input value or check network connectivity to the database service.
- If downstream nodes fail to parse results, connect to the JSON output and validate the structure (e.g., ensure it contains a 'tables' array).
