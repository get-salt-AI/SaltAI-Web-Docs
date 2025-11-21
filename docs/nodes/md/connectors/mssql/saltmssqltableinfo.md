# SQL Server Table Info

Retrieves detailed metadata for a specific Microsoft SQL Server table. It loads database credentials, queries metadata for the given schema and table, and returns results in multiple output formats (plain text summary and raw JSON, with placeholders for rich formats).

## Usage

Use this node when you need to inspect the structure of a SQL Server table before writing queries or building data pipelines. Typical workflows include validating column names and types, checking keys and nullability, or documenting a table prior to transformations.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored SQL Server credentials configured for this environment. The node uses these credentials to authenticate.</td><td style="word-wrap: break-word;">/workspace/credentials/mssql.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the database operation before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The name of the target table to inspect.</td><td style="word-wrap: break-word;">users</td></tr>
<tr><td style="word-wrap: break-word;">schema</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The database schema containing the target table. Defaults to dbo if not specified.</td><td style="word-wrap: break-word;">dbo</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the table information (columns, data types, and other highlights).</td><td style="word-wrap: break-word;">Table Info: dbo.users - id: INT, NOT NULL (PK) - email: NVARCHAR(255), NULL - created_at: DATETIME2, NOT NULL</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON payload containing the full metadata response for the table.</td><td style="word-wrap: break-word;">{"table":"users","schema":"dbo","columns":[{"name":"id","type":"INT","nullable":false,"primary_key":true},{"name":"email","type":"NVARCHAR(255)","nullable":true}]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of the results (may be empty if not generated).</td><td style="word-wrap: break-word;"><h3>Table Info: dbo.users</h3><table>...</table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Excel (.xlsx) data containing the table info (may be empty if not generated).</td><td style="word-wrap: break-word;"><bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">PDF data containing the table info (may be empty if not generated).</td><td style="word-wrap: break-word;"><bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Default schema is dbo. If your table is under a different schema, set the schema input accordingly.
- Ensure the provided credentials have permission to read metadata for the target schema and table.
- The JSON output contains the most complete representation of metadata; text/HTML are summaries.
- Timeout governs the end-to-end metadata request; increase it for slower networks or large catalogs.
- Never include real secrets in the credentials path file; store them securely and reference the file path only.

## Troubleshooting
- Table not found: Verify the schema and table_name inputs (including correct casing and spelling).
- Permission denied: Confirm the database user in credentials has rights to read system catalog metadata.
- Connection timeout: Increase the timeout input and check network/firewall connectivity to the SQL Server host.
- Invalid credentials: Re-upload or correct the credentials file at credentials_path and ensure it matches the MSSQL template.
- Empty outputs: Check logs and the JSON output for error details; misconfigured schema or missing permissions commonly cause empty results.
