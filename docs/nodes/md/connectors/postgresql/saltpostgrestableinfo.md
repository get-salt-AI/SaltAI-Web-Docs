# PostgreSQL Table Info

Retrieves structural information about a PostgreSQL table. Returns a human-readable summary and a JSON payload describing columns, data types, nullability, and related metadata for the specified table and schema.

## Usage

Use this node when you need to inspect a table’s structure before writing queries, building joins, or configuring downstream data processing. Typical workflows include schema exploration, validation of expected columns, and preparing inputs for visual or manual query builders.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database connection URI used to authenticate and connect to PostgreSQL.</td><td style="word-wrap: break-word;">postgresql://<username>:<password>@<host>:5432/<database>?sslmode=disable</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the table info request to complete.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the table to describe. Must exist in the provided schema.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database schema where the table resides.</td><td style="word-wrap: break-word;">public</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the table’s structure suitable for quick inspection.</td><td style="word-wrap: break-word;">Table Info: public.users Column: id \| Type: integer \| Nullable: NO \| Default: nextval('users_id_seq'::regclass) Column: email \| Type: text \| Nullable: NO ...</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the raw table metadata returned by the service.</td><td style="word-wrap: break-word;">{"columns":[{"column_name":"id","data_type":"integer","is_nullable":"NO","column_default":"nextval('users_id_seq'::regclass)"},{"column_name":"email","data_type":"text","is_nullable":"NO"}]}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of the results. For this node’s default output mode, this is empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX data of the results. For this node’s default output mode, this is empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF data of the results. For this node’s default output mode, this is empty.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials URI required**: Provide a valid PostgreSQL URI (e.g., postgresql://<username>:<password>@<host>:<port>/<database>) or the node will fail.
- **Schema and table must exist**: Ensure the specified schema and table are present and that the user has permission to read metadata.
- **Timeouts**: Long-running operations or network issues may require increasing the timeout value.
- **Secure secrets**: Never embed real credentials in shared workflows. Use placeholders or secure secret management.
- **Backend configuration**: This node relies on a configured backend database connector. If that service is not available, requests will fail.

## Troubleshooting
- **Invalid credentials URI**: If you see an error about an invalid or unsupported URI, verify the format and scheme (e.g., postgresql://) and ensure host, port, and database are correct.
- **Authentication or permission errors**: Confirm the username/password and that the role can access information_schema and the specified table.
- **Table not found**: Double-check the table_name and schema values. Use the PostgreSQL List Tables node to discover available tables.
- **Network/timeout issues**: Increase the timeout input or verify network connectivity to the database service.
- **Empty or partial metadata**: Some environments restrict metadata visibility. Ensure your role has required privileges to view column information.
