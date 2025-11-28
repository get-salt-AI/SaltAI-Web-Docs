# Oracle Table Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves structural information about a specific Oracle table. It loads Oracle credentials, optionally targets a schema, calls the Oracle service to fetch metadata, and returns both a human-readable summary and the raw JSON payload.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracletableinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to inspect a table’s structure before writing queries or building data workflows. Typical use: provide your Oracle credentials, the table name (and optionally the schema), then connect the outputs to logging, visualization, or downstream nodes that need table metadata (e.g., column names, data types).

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">CREDENTIALS</td><td style="word-wrap: break-word;">Path or reference to stored Oracle credentials that match the 'oracle' credential template.</td><td style="word-wrap: break-word;"><path-to-oracle-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the Oracle service request before failing.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">table_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of the Oracle table to retrieve information for.</td><td style="word-wrap: break-word;">employees</td></tr>
<tr><td style="word-wrap: break-word;">schema_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Oracle schema name that contains the table. Leave empty to use the current user’s default schema.</td><td style="word-wrap: break-word;"></td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary describing the table info request and result status.</td><td style="word-wrap: break-word;">Oracle Table Info: employees</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON metadata returned by the Oracle service (e.g., columns, data types, keys, constraints, indexes if available).</td><td style="word-wrap: break-word;">{'table': 'employees', 'schema': 'HR', 'columns': [{'name': 'EMPLOYEE_ID', 'type': 'NUMBER', 'nullable': False}, {'name': 'FIRST_NAME', 'type': 'VARCHAR2', 'nullable': True}], 'primary_key': ['EMPLOYEE_ID']}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node requires Oracle credentials configured under the 'oracle' credential template.
- If schema_name is left empty, the node queries the current user’s default schema.
- Timeout controls the maximum wait for the remote Oracle service; adjust for large schemas or slower networks.
- Returned JSON structure depends on the Oracle service and database metadata; available fields may include columns, data types, nullability, primary keys, and indexes.

## Troubleshooting
- Table not found: Verify table_name and schema_name are correct and exist in the target database.
- Insufficient privileges: Ensure the provided Oracle user has privileges to read table metadata.
- Connection or timeout errors: Increase the timeout value and confirm network access and service availability.
- Empty or partial metadata: Confirm the table exists and that the user can access system metadata views (e.g., ALL_TAB_COLUMNS).
