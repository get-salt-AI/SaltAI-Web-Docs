# MySQL Table Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves schema details for a specified MySQL table. Returns a human-readable summary and a JSON payload describing the table (e.g., columns, data types, and other available metadata). Uses provided database credentials and honors a configurable timeout.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqltableinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to inspect the structure of a MySQL table before querying, building joins, or validating data assumptions. Commonly placed early in a workflow to explore available columns and metadata for downstream query or transformation nodes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database connection URI for MySQL. The node uses this to authenticate and connect.</td><td style="word-wrap: break-word;">mysql://<username>:<password>@localhost:3306/sales_db</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the table to describe.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target database/schema containing the table.</td><td style="word-wrap: break-word;">mysql</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Readable summary of the table information, suitable for logs or quick review.</td><td style="word-wrap: break-word;">Table Info: sales_db.users Columns: - id (INT) - email (VARCHAR) - created_at (DATETIME) ...</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the detailed table metadata returned by the service.</td><td style="word-wrap: break-word;">{"data": [{"column":"id","type":"INT","nullable":false},{"column":"email","type":"VARCHAR(255)","nullable":false}]}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of results when available; for this operation it is typically empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX content when applicable; for this operation it is typically empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF content when applicable; for this operation it is typically empty.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials URI required**: You must provide a valid MySQL URI in credentials_path (e.g., "mysql://<username>:<password>@<host>:<port>/<database>").
- **Read-only operation**: This node fetches metadata only; it does not modify data.
- **Permissions matter**: The connected user must have privileges to read metadata for the specified database/table.
- **Timeout handling**: Long-running metadata calls will fail if they exceed the specified timeout.
- **Output formats**: This node primarily returns text and JSON; HTML/XLSX/PDF outputs are typically empty for table info.

## Troubleshooting
- **Authentication failed or invalid URI**: Verify credentials_path format and values. Ensure the user, password, host, port, and database are correct.
- **Table not found**: Confirm table_name and database are correct and that the table exists.
- **Insufficient privileges**: Ensure the user has permissions to access information_schema and the target table metadata.
- **Request timed out**: Increase the timeout input if the database is slow or under load, or optimize network connectivity.
- **Malformed JSON in output**: If downstream nodes parse json_result, ensure they parse it as a JSON string and handle empty/non-standard fields gracefully.
