# Oracle List Schemas

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node connects to an Oracle database via the configured Oracle CData service and retrieves the list of available schemas. It calls a backend "/list-schemas" operation and returns both human-readable text and structured JSON. The node is credential-driven and requires no additional query parameters, making it a straightforward way to discover schema names for downstream operations.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoraclelistschemas.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover which schemas exist in an Oracle database before querying tables or building more complex workflows. It typically appears early in a database workflow: first you configure Oracle credentials (using the oracle credential template), then run Oracle List Schemas to see all accessible schemas, and finally select one for use in nodes such as "Oracle List Tables", "Oracle Table Info", or "Oracle Visual Query Builder". Place it after any node that provides or selects a credentials file path for Oracle. Its outputs can be inspected directly or parsed by logic/control nodes to build interactive schema pickers, automated exploration pipelines, or dynamic query builders that adapt to the schemas actually available in the target database.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the saved Oracle credentials profile that matches the oracle credential template. The credentials must contain all necessary connection details such as host, port, service name or SID, username, and password recognized by the Oracle CData backend. The path must point to a valid and readable credentials file or object; otherwise, the node will fail when attempting to load credentials.</td><td style="word-wrap: break-word;">/workspace/credentials/cdata/oracle_hr_prod.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the Oracle /list-schemas request to complete. Increase this if the database is remote, heavily loaded, or if network latency is high; very small values can cause premature timeouts.</td><td style="word-wrap: break-word;">60</td></tr>
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
<tr><td style="word-wrap: break-word;">text_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the Oracle schemas returned by the backend, suitable for display in logs or UI panels. Typically includes a title such as "Oracle Schemas" and a line-separated or bullet-style listing of schema names.</td><td style="word-wrap: break-word;">Oracle Schemas - HR - SALES - FINANCE - SYS - SYSTEM</td></tr>
<tr><td style="word-wrap: break-word;">json_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded string containing the raw result from the /list-schemas endpoint. This usually includes an array of schema names and may include metadata such as total count or status flags. Downstream nodes can parse this JSON to drive dynamic selection, branching, or automated schema exploration.</td><td style="word-wrap: break-word;">{"schemas":["HR","SALES","FINANCE","SYS","SYSTEM"],"count":5,"status":"success"}</td></tr>
<tr><td style="word-wrap: break-word;">html_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML-formatted representation of the schemas list, if produced by the base formatter. This is useful for embedding into dashboards, reports, or other rich UI components that accept HTML content.</td><td style="word-wrap: break-word;"><h2>Oracle Schemas</h2><ul><li>HR</li><li>SALES</li><li>FINANCE</li><li>SYS</li><li>SYSTEM</li></ul></td></tr>
<tr><td style="word-wrap: break-word;">file_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to any exported file generated by the base node’s default formatter, such as a CSV or spreadsheet of schemas. For this simple list operation this may be empty, depending on configuration and formatter behavior.</td><td style="word-wrap: break-word;">/workspace/exports/oracle_schemas_2024-04-20.xlsx</td></tr>
<tr><td style="word-wrap: break-word;">extra_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional auxiliary data or metadata slot reserved by the base database node’s output contract. Often unused for basic list operations and may simply contain an empty string.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Listing schemas is usually fast, but on large multi-tenant instances or over slow networks you may need to raise the timeout to avoid failures.
- **Limitations**: The returned schemas are limited to what the Oracle user in the provided credentials is allowed to see; schemas without privileges will not appear in the results.
- **Behavior**: This node accepts no filtering or schema-selection arguments; it always requests the full set of visible schemas via the /list-schemas backend endpoint.
- **Behavior**: Output formatting (text, JSON structure, any file exports) is handled by the shared DatabaseBaseNode formatter, so exact JSON fields and file behavior are determined by that common layer rather than by this node alone.

## Troubleshooting
- **Connection or authentication failure**: If errors mention connection refused, invalid credentials, or unreachable host, verify that credentials_path points to a valid oracle credential file and that its host, port, and service name or SID are correct and reachable from the runtime environment.
- **Timeout exceeded**: If the node fails with a timeout, increase the timeout input value and check for network latency, VPNs, or firewalls that may delay the Oracle response.
- **Empty or unexpectedly small schema list**: When the node runs successfully but returns no or very few schemas, review the Oracle user’s permissions; the account may not have access to system or other application schemas.
- **Downstream JSON parsing errors**: If subsequent nodes fail when consuming json_output, log or inspect the raw json_output string to confirm it is valid JSON and adjust downstream parsing to the actual keys and structure returned by the Oracle service.
