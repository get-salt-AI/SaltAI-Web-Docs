# Neo4J Database Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node retrieves general metadata and status information about a Neo4J database through the Salt Neo4J service. It loads Neo4J credentials, calls a database-info endpoint with a configurable timeout, and returns the result formatted as human-readable text plus structured JSON (and related formats). It is intended for quick inspection, health checks, and understanding the configuration of the target Neo4J database without modifying any data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jdatabaseinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to confirm details about the Neo4J database you are connected to before running queries or schema operations. Typical scenarios include verifying that your workflow is pointed at the correct database instance, checking whether the database is online, and capturing metadata such as database name or role for logging or monitoring.

Place this node early in a graph-oriented workflow, usually right after selecting or configuring credentials and before query or write nodes such as Neo4J Query, Neo4J Write, Neo4J Nodes by Label, or Neo4J Graph Explorer. It is often combined with Neo4J Test Connection (to verify basic connectivity) and Neo4J Schema Info (to examine constraints and indexes) when setting up or diagnosing database integrations.

A common pattern is: configure credentials → run Neo4J Test Connection → run Neo4J Database Info to capture database metadata → proceed with Neo4J Node Labels, Neo4J Relationship Types, or Neo4J Graph Explorer for more in-depth analysis. Adjust the timeout based on expected network latency and database load, and have downstream logic parse the JSON output to make decisions based on fields like database status or role.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or identifier used by the platform to locate stored Neo4J credentials. These credentials must be configured under the neo4j template and must allow at least read access to database metadata. If the path is invalid or the credentials are missing, the node will return an error response instead of database info.</td><td style="word-wrap: break-word;">/project/secrets/neo4j/prod_credentials.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the Neo4J service to return database information. If the request takes longer than this value, the operation fails with a timeout error. Use smaller values (e.g., 10–30) for local or low-latency environments and larger values for remote or heavily loaded databases.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the Neo4J database information. Intended for quick inspection in logs or UI panels and typically includes database name, status, and other key properties formatted as plain text.</td><td style="word-wrap: break-word;">Neo4J Database Info Name: neo4j Status: online Default: true Role: primary Store size: 145 MB Last started: 2026-04-19T10:15:32Z</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded string containing the raw database information returned by the Neo4J service. Downstream nodes can parse this value to programmatically access properties such as name, status, role, or size metrics.</td><td style="word-wrap: break-word;">{"name": "neo4j", "status": "online", "default": true, "role": "primary", "store_size_bytes": 152674304, "last_started": "2026-04-19T10:15:32Z"}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML-formatted representation of the database info, often rendered as a simple table or structured block. Useful for embedding database information into dashboards or any component that can render HTML content.</td><td style="word-wrap: break-word;"><h2>Neo4J Database Info</h2><table><tr><th>Name</th><td>neo4j</td></tr><tr><th>Status</th><td>online</td></tr><tr><th>Role</th><td>primary</td></tr><tr><th>Store size</th><td>145 MB</td></tr></table></td></tr>
<tr><td style="word-wrap: break-word;">file</td><td style="word-wrap: break-word;">FILE</td><td style="word-wrap: break-word;">File reference produced by the base database node formatting logic, which may contain an exported report of the database info (such as HTML or text). This can be passed to downstream nodes that handle files for archiving, emailing, or further processing.</td><td style="word-wrap: break-word;">file://outputs/reports/neo4j_database_info_2026-04-20T12-00-00Z.html</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node issues a single metadata request, which is usually quick, but latency can increase with slow networks or heavily loaded servers; if you see intermittent timeouts, raise the timeout value slightly.
- **Limitations**: The exact structure of the database-info payload depends on the backend Neo4J service and configuration; some environments may expose more or fewer fields than shown in the examples.
- **Behavior**: When credentials are invalid or the Neo4J instance is unreachable, the node still returns formatted output, but the JSON content will typically contain an error description; always check for error-related fields before using metadata programmatically.
- **Behavior**: This node is strictly read-only and does not modify graph data or schema, making it suitable for use in production monitoring or diagnostic pipelines without risk to stored data.

## Troubleshooting
- **Connection timeout**: If the node fails with a timeout error, increase the timeout input and verify network connectivity to the Neo4J host (checking VPN, firewalls, and DNS resolution where applicable).
- **Authentication or authorization errors**: Messages indicating unauthorized access or authentication failure usually mean the credentials at credentials_path are incorrect or do not have sufficient rights; update the stored credentials and try again.
- **Unexpected empty or minimal info**: If the text output is very short and the JSON lacks expected fields like name or status, inspect the JSON for an error or message field, and verify that the Neo4J service supports and exposes the database-info endpoint.
- **Service endpoint or routing issues**: If the response indicates that the route is not found or the service is unavailable, confirm that the neo4j service is correctly configured in your environment and that the database-info route is enabled on the backend service.
