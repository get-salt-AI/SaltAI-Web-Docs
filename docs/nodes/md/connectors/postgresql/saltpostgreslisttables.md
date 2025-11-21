# PostgreSQL List Tables

Lists all tables available in a specified PostgreSQL schema. Uses configured PostgreSQL credentials to query the database and returns a human-readable summary along with structured JSON of the results.

## Usage

Use this node when you need to discover or verify the tables present in a particular PostgreSQL schema before building queries or data pipelines. Commonly placed early in workflows to explore database structure or to validate access and schema names.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the stored PostgreSQL credentials that the node will use to authenticate.</td><td style="word-wrap: break-word;"><credentials-file-path></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the list operation before failing.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target PostgreSQL schema from which to list tables.</td><td style="word-wrap: break-word;">public</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Readable summary of the tables found in the specified schema.</td><td style="word-wrap: break-word;">Tables in Schema: public - users - orders - products</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing structured results from the list operation.</td><td style="word-wrap: break-word;">{"data": [{"table_name": "users"}, {"table_name": "orders"}], "row_count": 2}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of the results (may be empty when using default text output).</td><td style="word-wrap: break-word;"><table><thead><tr><th>table_name</th></tr></thead><tbody><tr><td>users</td></tr></tbody></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary Excel data containing the results (may be empty when using default text output).</td><td style="word-wrap: break-word;"><binary-xlsx-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF data containing the results (may be empty when using default text output).</td><td style="word-wrap: break-word;"><binary-pdf-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: You must supply a valid PostgreSQL credential reference via credentials_path.
- **Schema value**: The schema must exist and the provided user must have permission to access it.
- **Timeouts**: Large schemas or slow connections may require increasing the timeout to avoid premature failures.
- **Output formats**: The default operation returns text and JSON; HTML/XLSX/PDF fields may be empty unless explicitly produced by the underlying service.
- **Service dependency**: The node relies on a backend database service endpoint for execution.

## Troubleshooting
- **No tables returned**: Verify the schema name and ensure the user has privileges to list tables in that schema.
- **Authentication error**: Check credentials_path points to a valid credential and that it contains correct host, port, database, username, and password.
- **Timeout error**: Increase the timeout input value, ensure network connectivity, and verify the database is responsive.
- **Malformed JSON in output**: Treat the json output as a string; parse it in a downstream step to access fields programmatically.
- **Permission denied**: Confirm the database user has USAGE/SELECT privileges on the target schema.
