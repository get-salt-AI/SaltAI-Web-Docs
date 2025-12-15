# Oracle Table Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves metadata for a specific Oracle table. It loads Oracle credentials, calls the table-info endpoint with the provided table and optional schema, and returns a human-readable summary plus a JSON payload of the table details.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracletableinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to inspect the structure of a table in an Oracle database (e.g., columns, data types, and related metadata). Typically placed early in a data workflow to validate schemas before building queries, transformations, or reports.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Path or handle to the stored Oracle credentials used for authentication.</td><td style="word-wrap: break-word;"><path-to-oracle-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Maximum time to wait for the operation to complete before failing.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The name of the Oracle table to analyze.</td><td style="word-wrap: break-word;">employees</td></tr>
<tr><td style="word-wrap: break-word;">schema_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema name containing the table. Leave empty to use the current user schema.</td><td style="word-wrap: break-word;">HR</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A readable summary describing the Oracle table information request and results.</td><td style="word-wrap: break-word;">Oracle Table Info: employees</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured JSON containing table metadata returned by the service (e.g., columns, data types, nullable flags, indexes if provided).</td><td style="word-wrap: break-word;">{"table":"employees","columns":[{"name":"EMPLOYEE_ID","type":"NUMBER","nullable":false}]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Schema handling**: If schema_name is left empty, the current user schema is used by default.
- **Credentials required**: Valid Oracle credentials are required; the node uses an Oracle credential template.
- **Timeout behavior**: The operation will fail if the service does not respond within the specified timeout.
- **Result format**: The node returns both a human-readable text summary and a JSON payload of the metadata.

## Troubleshooting
- **Table not found**: Verify table_name is correct and exists in the specified schema; ensure schema_name is correct or left empty to target the current user schema.
- **Insufficient privileges**: Confirm the Oracle user has privileges to access the specified schema and table.
- **Invalid credentials**: Recheck the credentials_path and that the stored credentials are valid and up-to-date.
- **Network/timeout errors**: Increase the timeout value or check connectivity to the Oracle service endpoint.
- **Malformed inputs**: Ensure table_name and schema_name are valid strings without unsupported characters.
