# Oracle Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a valid Oracle database connection URI from individual parameters. Supports service name or SID style connections and optionally appends character encoding. Returns the constructed URI as the primary output; other outputs are unused placeholders.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracleconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to generate a single Oracle connection string to feed into other Oracle database nodes as the credentials_path. Typical workflow: configure host, port, service/service SID, username and password (and optionally encoding), run this node, then connect its 'result' output to subsequent Oracle query or management nodes.

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
<tr><td style="word-wrap: break-word;">host</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database hostname or IP address.</td><td style="word-wrap: break-word;">db.company.com</td></tr>
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Database port number.</td><td style="word-wrap: break-word;">1521</td></tr>
<tr><td style="word-wrap: break-word;">service_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Oracle service name or SID (value is used for both; behavior depends on connection_type).</td><td style="word-wrap: break-word;">XE</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database username.</td><td style="word-wrap: break-word;">system</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Database password corresponding to the username.</td><td style="word-wrap: break-word;"><password></td></tr>
<tr><td style="word-wrap: break-word;">connection_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select whether to connect using a service name or a SID.</td><td style="word-wrap: break-word;">service_name</td></tr>
<tr><td style="word-wrap: break-word;">encoding</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Character encoding to append to the URI. If left as UTF-8 (default), no parameter is added.</td><td style="word-wrap: break-word;">UTF-8</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The constructed Oracle connection URI. This is the value you pass as credentials_path to other nodes.</td><td style="word-wrap: break-word;">oracle://system:<password>@db.company.com:1521/XE</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON output used only when an error occurs; otherwise empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Not used by this node; returned as an empty string.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Not used by this node; returned as an empty string.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Not used by this node; returned as an empty string.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- If connection_type is 'sid', the URI includes '?sid=<service_name>'; otherwise it uses the service name path segment without the sid parameter.
- The encoding parameter is only appended when it is not 'UTF-8'.
- Special characters in usernames or passwords may require URL-safe encoding before use to avoid malformed URIs.
- This node does not test connectivity; it only constructs the string. Use an Oracle test/connection node to validate.
- The password is embedded in the URI. Handle and store this output securely.

## Troubleshooting
- Connection failures in downstream nodes: Verify host, port, and whether your database expects a service name vs a SID. Switch the connection_type accordingly.
- Authentication errors: Confirm username/password are correct and URL-safe (encode characters like @, :, /, or spaces).
- Invalid service or SID: Ensure the 'service_name' matches your Oracle configuration (e.g., service alias in tnsnames.ora).
- Character encoding issues: If you see garbled characters, try setting a specific encoding (e.g., 'AL32UTF8') and regenerate the URI.
- Downstream node shows empty credentials: Make sure you wired this node's 'result' output to the 'credentials_path' input of the target node.
