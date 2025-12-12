# Oracle Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds an Oracle database connection URI from individual parameters. It supports both service name and SID styles and optionally appends a non-default character encoding. The node only constructs the string; it does not test connectivity.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracleconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a correctly formatted Oracle URI to feed into other Salt Oracle database nodes as the credentials_path. Typical workflow: provide host, port, service/SID, username, password, choose connection type, and then pass the resulting URI to query, execute, or info nodes.

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
<tr><td style="word-wrap: break-word;">host</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Oracle database hostname or IP address.</td><td style="word-wrap: break-word;">db.mycompany.internal</td></tr>
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Oracle database TCP port.</td><td style="word-wrap: break-word;">1521</td></tr>
<tr><td style="word-wrap: break-word;">service_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Oracle service name or SID value (used in the path segment and, for SID connections, also appended as a sid parameter).</td><td style="word-wrap: break-word;">XE</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database username used for authentication.</td><td style="word-wrap: break-word;">system</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Database password used for authentication.</td><td style="word-wrap: break-word;"><password></td></tr>
<tr><td style="word-wrap: break-word;">connection_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Connection style selector. Allowed values: "service_name" or "sid". For "sid", the URI includes a sid query parameter.</td><td style="word-wrap: break-word;">service_name</td></tr>
<tr><td style="word-wrap: break-word;">encoding</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Character encoding for the connection. Only added to the URI if not "UTF-8".</td><td style="word-wrap: break-word;">UTF-8</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The constructed Oracle connection URI, suitable for use as credentials_path in other nodes.</td><td style="word-wrap: break-word;">oracle://system:<password>@db.mycompany.internal:1521/XE</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON payload with error details if construction fails; empty string on success.</td><td style="word-wrap: break-word;">{"error":"Failed to construct connection string: <details>"}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Not used by this node; empty string.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Not used by this node; empty string.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Not used by this node; empty string.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Password visibility**: The resulting URI embeds the username and password in plain text. Handle and store it securely.
- **Encoding behavior**: The encoding parameter is only appended if it differs from "UTF-8".
- **Service name vs SID**: If connection_type is "sid", the node appends ?sid=<service_name> to the URI path.
- **Special characters**: The node does not URL-encode credentials. If your username or password contains special characters (e.g., @, :, /, ?), URL-encode them before input to avoid malformed URIs.
- **No connectivity check**: This node does not test the database connection; it only constructs the string.

## Troubleshooting
- **Constructed URI causes downstream connection failures**: Verify host, port, and whether the target uses a service name or SID. Ensure the Oracle listener is up and the service/SID is correct.
- **Errors about malformed URI**: Avoid special characters in username/password or pre-encode them using URL encoding.
- **Unexpected encoding issues**: If your database requires a different encoding, set the encoding input to that value so it is appended as a query parameter.
- **Received 'Failed to construct connection string'**: Check that all required inputs are provided and correctly typed (port is an integer, connection_type is either "service_name" or "sid").
