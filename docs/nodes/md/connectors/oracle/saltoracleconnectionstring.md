# Oracle Connection String

Builds a valid Oracle database connection URI from separate parameters. Supports both service name and SID styles, and can append a custom character encoding. Intended to simplify passing a single, correctly formatted connection string to downstream database nodes.

## Usage

Use this node when you need to assemble an Oracle connection string from host, port, service/service_name (or SID), username, and password. Typically placed before nodes that accept a connection string for querying or executing commands against an Oracle database.

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
<tr><td style="word-wrap: break-word;">host</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database hostname or IP address.</td><td style="word-wrap: break-word;">db.company.internal</td></tr>
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Oracle listener port. Must be between 1 and 65535.</td><td style="word-wrap: break-word;">1521</td></tr>
<tr><td style="word-wrap: break-word;">service_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Oracle service name or SID to connect to. Used for both connection types; for SID connections it is appended as a sid query parameter.</td><td style="word-wrap: break-word;">XE</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database username.</td><td style="word-wrap: break-word;">system</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Database password for the specified user.</td><td style="word-wrap: break-word;"><oracle-password></td></tr>
<tr><td style="word-wrap: break-word;">connection_type</td><td>True</td><td style="word-wrap: break-word;">["service_name", "sid"]</td><td style="word-wrap: break-word;">Choose how to form the connection: service_name uses the standard path segment; sid adds a sid query parameter.</td><td style="word-wrap: break-word;">service_name</td></tr>
<tr><td style="word-wrap: break-word;">encoding</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional character encoding to append to the URI. If set to UTF-8 (default), it is omitted.</td><td style="word-wrap: break-word;">AL32UTF8</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The constructed Oracle connection string.</td><td style="word-wrap: break-word;">oracle://system:<oracle-password>@db.company.internal:1521/XE</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">JSON output (unused when successful; contains error details if construction fails).</td><td style="word-wrap: break-word;">{"error": "Failed to construct connection string: <reason>"}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML output placeholder (empty for this node).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">XLSX output placeholder (empty for this node).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF output placeholder (empty for this node).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Output format**: Only the text output is meaningful on success; other outputs remain empty.
- **Service name vs SID**: When connection_type is "sid", the node appends "?sid=<service_name>" to the URI path; otherwise it uses the service name in the standard path segment.
- **Encoding parameter**: The encoding query parameter is only added if it differs from "UTF-8".
- **Security**: Never hard-code real credentials in shared workflows. Use secure credential management where possible.
- **Example URI structure**: service_name: oracle://username:password@host:port/SERVICE; sid: oracle://username:password@host:port/SERVICE?sid=SERVICE

## Troubleshooting
- **Invalid or empty password produces connection failures downstream**: Confirm the password input is correct and not blank.
- **Wrong connection_type**: If you see authentication or service errors, try switching between service_name and sid depending on your Oracle setup.
- **Bad host/port**: If clients cannot reach the database, verify hostname resolution and that the port (default 1521) is open and correct.
- **Encoding issues**: If you observe garbled characters, set encoding to the correct Oracle charset (e.g., AL32UTF8) instead of UTF-8.
- **Error output**: If construction fails, check the json output for an "error" message detailing the reason.
