# MySQL List Tables

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lists all tables available in a specified MySQL database. It connects using provided credentials, calls a list-tables operation, and returns both a readable summary and a JSON payload of the results.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqllisttables.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover or enumerate tables within a MySQL database before constructing queries or fetching table-specific information. Typical workflows include: selecting a database with MySQL List Databases, listing its tables with this node, then feeding a table name into a node like MySQL Table Info or a visual/query builder.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to saved MySQL credentials to authenticate the request.</td><td style="word-wrap: break-word;">/path/to/credentials/mysql.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation to complete before failing.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the MySQL database from which to list tables.</td><td style="word-wrap: break-word;">sales_db</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the tables found in the specified database.</td><td style="word-wrap: break-word;">Tables in Database: sales_db - customers - orders - products</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded result containing the raw response data for all listed tables.</td><td style="word-wrap: break-word;">{"data": [{"table_name": "customers"}, {"table_name": "orders"}]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of the results (empty for default output mode).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary Excel data of the result set (empty for default output mode).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Binary PDF data of the result set (empty for default output mode).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: Ensure the credentials provided have permissions to read metadata and list tables in the specified database.
- **Database name is case-sensitive in some environments**: Provide the exact database name to avoid empty results.
- **Timeout applies to the request**: If the database is slow or large, consider increasing the timeout to prevent premature failures.
- **Output formats**: By default, the node returns text and JSON; HTML/XLSX/PDF outputs are empty unless another node explicitly formats/exports the results.
- **Service targeting**: This node targets a MySQL service profile; ensure the credentials match the intended MySQL instance.

## Troubleshooting
- **No tables returned**: Verify the database name is correct and the credentials have sufficient privileges to view table metadata.
- **Authentication or connection failure**: Check the credentials_path points to valid MySQL credentials and that network/firewall settings allow access.
- **Operation timed out**: Increase the timeout value and retry; also confirm the database is responsive.
- **Permission denied errors**: Ask your database administrator to grant metadata/list privileges for the specified database.
