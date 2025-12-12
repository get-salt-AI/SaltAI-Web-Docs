# MySQL Table Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves detailed information about a specific MySQL table within a given database. It returns a human-readable summary and a JSON payload describing columns, data types, keys, and other table metadata.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqltableinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to understand a table’s structure before building queries, data transformations, or integrations. Typical workflows include exploring schema details during data discovery, validating table structures prior to query execution, and documenting database schemas.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the stored MySQL credentials to authenticate the connection.</td><td style="word-wrap: break-word;">/workspace/credentials/mysql.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before it times out.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The name of the table to retrieve information about.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The database name that contains the target table.</td><td style="word-wrap: break-word;">analytics_db</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A formatted, human-readable summary of the table’s schema and key attributes.</td><td style="word-wrap: break-word;">Table Info: analytics_db.users - id: INT (PRIMARY KEY) - email: VARCHAR(255) (UNIQUE) - created_at: DATETIME Total columns: 12</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string containing the raw table metadata, including columns, types, nullability, defaults, and keys.</td><td style="word-wrap: break-word;">{"table":"users","database":"analytics_db","columns":[{"name":"id","type":"INT","nullable":false,"primary_key":true},{"name":"email","type":"VARCHAR(255)","nullable":false,"unique":true}]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML output (empty for this node’s default behavior).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Excel data (empty for this node’s default behavior).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">PDF data (empty for this node’s default behavior).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: You must provide a valid credentials_path for a MySQL connection.
- **Database default**: If not changed, the default database value is "mysql"; update it to your target schema.
- **Output formats**: By default, the node returns a text summary and JSON payload; other formats are left empty.
- **Table existence**: The table_name must exist in the specified database and be accessible with the provided credentials.
- **Timeouts**: Long-running metadata operations may require increasing the timeout to avoid premature failures.

## Troubleshooting
- **Connection or authentication errors**: Verify credentials_path points to valid MySQL credentials and that network access to the database is allowed.
- **Table not found**: Ensure table_name is correct and exists within the specified database.
- **Insufficient permissions**: Confirm the provided user has privileges to read metadata in the target database.
- **Timeouts**: Increase the timeout value for slow or busy databases and confirm network latency is acceptable.
- **Malformed inputs**: Ensure database and table_name are non-empty strings and do not include unsupported characters.
