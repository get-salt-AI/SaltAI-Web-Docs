# Oracle Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds an Oracle database connection URI from individual parameters. Supports both service name and SID connection types, and optionally appends a custom character encoding. Returns the constructed connection string in the node’s primary text output slot.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracleconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a correctly formatted Oracle connection URI to pass into downstream database nodes or any component that accepts a connection string. Typical workflow: provide host, port, service name or SID, and credentials; choose the connection type; optionally set a non-default encoding; then connect the resulting string to nodes that initiate database operations.

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
<tr><td style="word-wrap: break-word;">host</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database hostname or IP address where the Oracle instance is reachable.</td><td style="word-wrap: break-word;">db.example.com</td></tr>
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">TCP port for the Oracle listener. Valid range is 1–65535.</td><td style="word-wrap: break-word;">1521</td></tr>
<tr><td style="word-wrap: break-word;">service_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Oracle service name or SID, depending on the selected connection type.</td><td style="word-wrap: break-word;">ORCLPDB1</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database user for authentication.</td><td style="word-wrap: break-word;">system</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Password for the specified database user.</td><td style="word-wrap: break-word;"><password></td></tr>
<tr><td style="word-wrap: break-word;">connection_type</td><td>True</td><td style="word-wrap: break-word;">["service_name", "sid"]</td><td style="word-wrap: break-word;">Choose whether the value provided is a service name or a SID. Determines how the URI is formed.</td><td style="word-wrap: break-word;">service_name</td></tr>
<tr><td style="word-wrap: break-word;">encoding</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional character encoding appended as a query parameter when not UTF-8.</td><td style="word-wrap: break-word;">AL32UTF8</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary output containing the constructed Oracle connection string (URI).</td><td style="word-wrap: break-word;">oracle://system:<password>@db.example.com:1521/ORCLPDB1</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Auxiliary JSON output. Not used by this node (empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Auxiliary HTML output. Not used by this node (empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Auxiliary Excel output. Not used by this node (empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Auxiliary PDF output. Not used by this node (empty).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- If connection_type is "sid", the node appends a sid parameter and still uses the same service_name value in the path component.
- The encoding parameter is only added to the URI when it differs from UTF-8.
- Ensure usernames and passwords with special characters are URL-safe; if needed, URL-encode credentials before use.
- This node does not test connectivity; it only formats the connection string.

## Troubleshooting
- Constructed string fails to connect: Verify host, port, and that connection_type matches your database configuration (service name vs SID).
- Authentication errors: Confirm username/password and check if special characters require URL-encoding.
- Invalid service name/SID: Confirm the exact service name or SID from your Oracle listener configuration.
- Unexpected character encoding behavior: Leave encoding as UTF-8 (default) unless your environment requires a different value.
