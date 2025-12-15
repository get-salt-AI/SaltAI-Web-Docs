# Neo4J Database Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves high-level information about the connected Neo4J database via the Salt data service. Returns a human-readable summary along with the raw JSON payload. Typical details include version, edition, default database, and basic server/cluster metadata.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jdatabaseinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to verify connectivity and inspect your Neo4J environment before running queries or schema operations. It fits early in a workflow to validate credentials and understand the target database characteristics, or in monitoring/diagnostics flows to capture current database state.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">CREDENTIALS</td><td style="word-wrap: break-word;">Path or reference to stored Neo4J credentials configured for the Salt data service. The credentials must be associated with the 'neo4j' service profile.</td><td style="word-wrap: break-word;"><path-or-secret-ref-to-neo4j-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the data service to return database information before timing out.</td><td style="word-wrap: break-word;">60</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">TEXT</td><td style="word-wrap: break-word;">A readable summary of the Neo4J database information.</td><td style="word-wrap: break-word;">Neo4J Database Info Version: 5.x Edition: Community Default Database: neo4j Cluster: Single instance</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON payload with database information as returned by the service.</td><td style="word-wrap: break-word;">{"version":"5.x","edition":"Community","default_database":"neo4j","server":{"addresses":["bolt://localhost:7687"]}}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML content (if available for this call). For this node, it is typically empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Binary Excel data (if available for this call). For this node, it is typically empty.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">Binary PDF data (if available for this call). For this node, it is typically empty.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials**: Ensure the provided credentials reference is valid and mapped to the 'neo4j' service profile.
- **Network access**: The Salt data service must be able to reach your Neo4J endpoint (host, port, and protocol).
- **Timeouts**: Increase the timeout for slow or remote environments to avoid premature failures.
- **Read permissions**: The credentials must have sufficient permissions to query database metadata.

## Troubleshooting
- **Authentication failed**: Verify username/password or token in the credentials reference and that it targets the correct Neo4J instance.
- **Connection error / timeout**: Check network/firewall rules, host/port, protocol (bolt/neo4j with or without TLS), and increase the timeout input.
- **Empty or partial info**: Ensure the connected user has permissions to read database and system metadata.
- **Service unavailable**: Confirm the Salt data service is running and healthy, and retry once it is reachable.
