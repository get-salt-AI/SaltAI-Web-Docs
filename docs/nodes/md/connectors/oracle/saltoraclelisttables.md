# Oracle List Tables

Lists all tables available in an Oracle schema using the provided database credentials. If no schema is specified, it lists tables from the current user's default schema. Returns a human-readable summary along with structured JSON data.

## Usage

Use this node to explore the available tables before building queries or performing data operations. Typical workflow: test connection -> list schemas (optional) -> list tables (this node) -> get table info or run queries.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored Oracle credential configuration that includes connection details.</td><td style="word-wrap: break-word;">/configs/credentials/oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before it is aborted.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">schema_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target Oracle schema to list tables from. Leave empty to list tables in the current user's default schema.</td><td style="word-wrap: break-word;">HR</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A formatted, human-readable summary of the listed tables, including the schema context.</td><td style="word-wrap: break-word;">Oracle Tables in Schema: HR Total: 7</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Structured result containing the list of tables and related metadata.</td><td style="word-wrap: break-word;">{"tables": ["EMPLOYEES", "DEPARTMENTS", "JOBS"], "schema": "HR"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Schema handling**: Leaving schema_name empty will list tables from the current user's default schema.
- **Permissions**: The connected user must have privileges to read metadata for the specified schema; otherwise, the list may be empty or error.
- **Case sensitivity**: Oracle stores unquoted identifiers in uppercase; table names in results may be uppercase.
- **Large schemas**: Listing a very large number of tables may take longer; consider increasing the timeout if needed.

## Troubleshooting
- **Empty results**: Verify schema_name is correct and the user has permissions to access that schema.
- **Timeouts**: Increase the timeout input value if the database is slow or the schema is large.
- **Authentication errors**: Ensure credentials_path points to a valid Oracle credential configuration and the credentials are correct.
- **Network connectivity**: If requests fail, confirm network access to the Oracle database and any required gateways or services.
