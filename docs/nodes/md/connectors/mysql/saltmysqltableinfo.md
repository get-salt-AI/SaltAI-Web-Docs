# MySQL Table Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves structural information for a specified MySQL table. Returns a human-readable summary along with a JSON payload that typically includes columns, data types, nullability, and other metadata. Uses provided credentials to connect and queries a backend service for the table details.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqltableinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to inspect the schema of a table before writing queries or building data pipelines. Commonly placed early in a workflow to validate column names, data types, and keys prior to running queries or transformations.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the stored MySQL credentials to authenticate the request.</td><td style="word-wrap: break-word;">/configs/credentials/mysql.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before it times out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the MySQL table to retrieve metadata for.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">MySQL database (schema) containing the table.</td><td style="word-wrap: break-word;">analytics_db</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A readable summary of the table information, suitable for logs or quick inspection.</td><td style="word-wrap: break-word;">Table Info: analytics_db.users - id (INT, NOT NULL) - email (VARCHAR(255), NULL) - created_at (DATETIME, NOT NULL) ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing the raw table metadata returned by the service (columns, types, and related attributes).</td><td style="word-wrap: break-word;">{"columns": [{"name": "id", "type": "INT", "nullable": false}, {"name": "email", "type": "VARCHAR(255)", "nullable": true}]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML content (if provided for this operation). For this node it is typically empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary Excel data (if provided for this operation). For this node it is typically empty.</td><td style="word-wrap: break-word;"><binary data></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF data (if provided for this operation). For this node it is typically empty.</td><td style="word-wrap: break-word;"><binary data></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: Ensure the credentials_path points to valid MySQL credentials; the node will load and use them before making the request.
- **Database and table must exist**: The specified database and table_name must be accessible by the provided user.
- **Timeouts**: Operations that exceed the provided timeout will fail; increase the timeout for large or slow databases.
- **Result formats**: The text and JSON outputs are the primary outputs for this node; other formats (HTML/XLSX/PDF) will generally be empty for this operation.
- **Service interaction**: The node relies on a backend service endpoint for table metadata; network connectivity is required.

## Troubleshooting
- **Authentication error**: Verify credentials_path points to a valid file/reference and the credentials are correct.
- **Table not found**: Confirm table_name is correct and exists in the specified database. Check for typos or case sensitivity.
- **Insufficient privileges**: Ensure the database user has rights to read metadata for the target table.
- **Network or timeout issues**: Increase the timeout input and verify network connectivity to the database service.
- **Malformed output parsing**: If JSON output cannot be parsed downstream, inspect the raw json output field for error messages from the service.
