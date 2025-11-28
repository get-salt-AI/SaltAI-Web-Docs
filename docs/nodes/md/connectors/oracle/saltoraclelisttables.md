# Oracle List Tables

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lists all tables available in a specified Oracle schema. If no schema is provided, it lists tables in the current user’s default schema. Returns a human-readable summary and structured JSON of the discovered tables.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclelisttables.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to quickly inventory tables in an Oracle database when exploring data sources, validating access, or building subsequent queries. Typically placed early in a workflow to discover available tables before fetching columns or running queries against specific tables.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the stored Oracle database credentials to authenticate requests.</td><td style="word-wrap: break-word;"><path-to-oracle-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before timing out.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">schema_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target Oracle schema to list tables from. Leave empty to use the current user’s schema.</td><td style="word-wrap: break-word;">HR</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Readable summary of tables found in the schema.</td><td style="word-wrap: break-word;">Oracle Tables in Schema: HR Total: 12 - EMPLOYEES - DEPARTMENTS - LOCATIONS ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded result payload containing the list of tables and associated metadata (if provided by the service).</td><td style="word-wrap: break-word;">{"tables": ["EMPLOYEES", "DEPARTMENTS", "LOCATIONS"], "schema": "HR"}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation (if available). For this operation using default formatting, this is typically empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Serialized XLSX content or reference (if available). For this operation using default formatting, this is typically empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Serialized PDF content or reference (if available). For this operation using default formatting, this is typically empty.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- If schema_name is left empty, the node queries the current user’s default schema.
- Requires valid Oracle credentials; ensure the credentials file or reference is accessible and correctly formatted.
- The timeout applies to the remote request; large schemas or slow connections may require higher values.
- Output includes a text summary and a JSON string; other formats are generally empty for this operation.

## Troubleshooting
- No tables returned: Verify schema_name is correct or try leaving it empty to use the current user’s schema. Confirm the user has privileges to view tables.
- Authentication error: Check credentials_path and ensure the credentials are valid and permitted to list tables.
- Timeouts or network errors: Increase the timeout value and verify network connectivity to the Oracle service.
- Invalid schema name: Use the exact schema identifier as defined in the database, respecting case-sensitivity and permissions.
