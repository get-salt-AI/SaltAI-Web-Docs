# MySQL Test Connection

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Tests connectivity to a MySQL database using the provided connection URI. It sends a connection check to the configured MySQL data connector service and returns a human-readable summary and raw JSON response. Export fields (HTML/XLSX/PDF) are included in the outputs but are empty for this operation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mysql/saltmysqltestconnection.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to validate that your MySQL credentials, host, port, and database are correct and reachable before running queries or other database operations. Provide a valid MySQL URI and adjust the timeout if needed. Typical workflow: place this node early in a pipeline to ensure the connection is healthy before executing queries or schema operations.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">MySQL database URI containing host, port, database, and credentials. Must be a valid URI.</td><td style="word-wrap: break-word;">mysql://<user>:<password>@<host>:3306/<database></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Request timeout in seconds for the connection test. Must be between 10 and 300.</td><td style="word-wrap: break-word;">60</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the connection test result.</td><td style="word-wrap: break-word;">MySQL Connection Test: Success</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON response string from the service, containing connection test details.</td><td style="word-wrap: break-word;">{"status": "success", "message": "Connection established"}</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML export placeholder. Not used for this node; returned as an empty string.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">XLSX export placeholder (base64-encoded). Not used for this node; returned as an empty string.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">PDF export placeholder (base64-encoded). Not used for this node; returned as an empty string.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials must be a valid MySQL URI**: Use the mysql:// scheme. Invalid or unsupported schemes will raise an error.
- **Service configuration required**: The MySQL data connector service URL must be configured in the platform endpoints; otherwise the node will fail to locate the service.
- **Timeout constraints**: The timeout value must be between 10 and 300 seconds.
- **Outputs for exports are empty**: html_table, xlsx_data, and pdf_data are intentionally empty for connection testing.
- **No data is modified**: This node only validates connectivity and does not perform any read or write operations.

## Troubleshooting
- **Invalid credentials URI**: If you see an error about an invalid URI, ensure it follows mysql://<user>:<password>@<host>:<port>/<database> and contains necessary components.
- **Service URL not configured**: Errors mentioning missing service URL or endpoint mapping indicate the MySQL data connector endpoint is not set. Configure the MySQL connector in platform endpoints.
- **Connection timed out**: Increase the timeout input value, verify network access (host/port/firewall/VPC), and ensure the MySQL server is reachable.
- **Authentication failed**: Check username/password and database name in the URI. Verify SSL parameters if your server requires them (e.g., add query params to the URI).
- **Unsupported host or DNS issues**: Verify that the host resolves correctly and that the specified port (default 3306) is open.
