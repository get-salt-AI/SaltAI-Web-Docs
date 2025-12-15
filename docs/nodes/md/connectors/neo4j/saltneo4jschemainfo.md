# Neo4J Schema Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves high-level schema information from a Neo4J database, focusing on constraints and indexes. It authenticates using provided credentials, calls the Neo4J service, and returns a readable summary along with the raw JSON payload.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jschemainfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to inspect or audit the graph schema—such as reviewing existing indexes and constraints—before drafting queries, optimizing performance, or enforcing governance. Place it early in a workflow to understand the current graph structure, or alongside query/maintenance steps to verify schema changes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">CREDENTIALS</td><td style="word-wrap: break-word;">Path or reference to stored Neo4J credentials based on the 'neo4j' credential template. Must include endpoint/host, authentication, and any required configuration.</td><td style="word-wrap: break-word;"><path-to-neo4j-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Request timeout in seconds for the operation. Prevents long-running schema calls from blocking the workflow.</td><td style="word-wrap: break-word;">120</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the schema info (constraints and indexes).</td><td style="word-wrap: break-word;">Neo4J Schema Info: 3 indexes, 2 constraints found.</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON string containing the full schema information returned by the service.</td><td style="word-wrap: break-word;">{"indexes": [...], "constraints": [...]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: You must supply valid Neo4J credentials via credentials_path using the 'neo4j' template.
- **Read-only operation**: This node only reads schema metadata; it does not alter the database.
- **Timeout behavior**: If the database is slow or unavailable, the node may return a timeout error based on the configured timeout.
- **JSON structure**: The JSON payload contents (fields for indexes/constraints) may vary depending on your Neo4J version and backend service implementation.

## Troubleshooting
- **Authentication failed**: Verify credentials_path points to valid credentials and that the username/password or token is correct.
- **Connection refused or timed out**: Check network access to the Neo4J host/port and increase the timeout if needed.
- **Empty or partial results**: Ensure the authenticated user has sufficient privileges to view schema metadata.
- **Invalid JSON output**: If consuming the JSON downstream, ensure the consumer expects a JSON string and parses it before use.
