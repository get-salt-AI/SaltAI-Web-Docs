# Oracle Table Info

Retrieves metadata for a specified Oracle database table. It loads provided Oracle credentials, queries the database for the table’s structure, and returns a readable summary along with a JSON payload of the details.

## Usage

Use this node when you need to inspect a table’s structure before writing queries or building pipelines. Typical workflows include schema discovery, validating column names and data types, and preparing join conditions for downstream nodes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored Oracle database credentials configured in Salt.</td><td style="word-wrap: break-word;">salt://credentials/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before it times out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the Oracle table to retrieve information about.</td><td style="word-wrap: break-word;">employees</td></tr>
<tr><td style="word-wrap: break-word;">schema_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schema that contains the target table. Leave empty to use the current user’s schema.</td><td style="word-wrap: break-word;">HR</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the table information.</td><td style="word-wrap: break-word;">Oracle Table Info: employees Columns: EMPLOYEE_ID (NUMBER), FIRST_NAME (VARCHAR2), LAST_NAME (VARCHAR2), ...</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured metadata for the table, typically including columns, data types, nullability, and other attributes.</td><td style="word-wrap: break-word;">{"table":"EMPLOYEES","columns":[{"name":"EMPLOYEE_ID","data_type":"NUMBER","nullable":false},{"name":"FIRST_NAME","data_type":"VARCHAR2","nullable":true}]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML-rendered content for the results, if available. May be empty when using default formatting.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary Excel data for the results, if available. May be empty when using default formatting.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF data for the results, if available. May be empty when using default formatting.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: Ensure the credentials_path points to valid Oracle credentials with permissions to read metadata.
- **Schema handling**: If schema_name is empty, the node queries the current user’s default schema.
- **Case sensitivity**: Table and schema names may be case-sensitive depending on how they were created (quoted identifiers).
- **Access control**: Insufficient privileges can result in empty results or errors when inspecting table metadata.
- **Timeouts**: Long-running or overloaded databases may require increasing the timeout.

## Troubleshooting
- **No results returned**: Verify table_name and schema_name are correct and that the user has privileges to access the table.
- **Authentication errors**: Confirm credentials_path is correct and the stored credentials are valid.
- **Timeouts**: Increase the timeout value if the database is slow or under heavy load.
- **Invalid table name**: Check spelling and case; try fully qualifying with schema_name if not using the default schema.
- **Partial metadata**: Ensure the user has necessary dictionary/view access (e.g., permissions to read column metadata).
