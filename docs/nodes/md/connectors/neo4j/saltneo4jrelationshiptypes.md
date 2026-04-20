# Neo4J Relationship Types

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node calls the configured Neo4J CData service to fetch all relationship types currently defined in the target Neo4J database. It loads credentials, invokes the /relationship-types operation, and returns results formatted as human-readable text plus structured JSON. The node is read-only and intended for schema discovery and graph exploration workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jrelationshiptypes.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to inspect which relationship types exist in your Neo4J graph, for example before building Cypher queries or configuring downstream graph analytics. It fits early in pipelines that explore or document graph structure, typically alongside Neo4J Node Labels, Neo4J Schema Info, and Neo4J Database Info. Upstream, you provide a credentials reference for an existing Neo4J CData connector; downstream, pass the JSON output into logic or query-building nodes (for example, Neo4J Query or Neo4J Relationships by Type) or into UI components that let a user choose a relationship type from the discovered list. A common pattern is: run Neo4J Test Connection to validate connectivity, then Neo4J Relationship Types and Neo4J Node Labels to understand the schema, then use the selected relationship type to filter relationships or to parameterize Cypher queries in subsequent nodes. This node is preferable to custom Cypher introspection when you just need a clean list of relationship types without maintaining the query logic yourself.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reference or path to the stored Neo4J CData credentials. This must point to a credential set configured with the neo4j template (URI, authentication, and related options) so the node can connect to the correct Neo4J instance.</td><td style="word-wrap: break-word;">cdata/neo4j/prod-cluster-01</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of seconds to wait for the /relationship-types request to complete before failing. Must be a positive integer; increase this value if your Neo4J instance is remote, under load, or behind slower networking.</td><td style="word-wrap: break-word;">60</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable summary of the relationship types returned by Neo4J, suitable for logs or UI display. Typically includes a title and a line-per-type listing.</td><td style="word-wrap: break-word;">Neo4J Relationship Types: - KNOWS - PURCHASED - FOLLOWS - MEMBER_OF Total relationship types: 4</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded payload of the relationship types response from the CData Neo4J service. Usually contains an array of relationship type names and may include extra metadata depending on connector configuration.</td><td style="word-wrap: break-word;">{"relationship_types": ["KNOWS", "PURCHASED", "FOLLOWS", "MEMBER_OF"], "count": 4}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional HTML-formatted representation of the relationship types list, when enabled by the base formatting utilities. Can be embedded directly into dashboards or web views.</td><td style="word-wrap: break-word;"><h2>Neo4J Relationship Types</h2><ul><li>KNOWS</li><li>PURCHASED</li><li>FOLLOWS</li><li>MEMBER_OF</li></ul></td></tr>
<tr><td style="word-wrap: break-word;">binary</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Optional binary export of the results (for example, an XLSX or PDF file) if the underlying DatabaseBaseNode is configured to generate file outputs. This may be empty when exports are not requested.</td><td style="word-wrap: break-word;">Not specified</td></tr>
<tr><td style="word-wrap: break-word;">extra</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional auxiliary information or metadata encoded as a string, reserved for advanced scenarios by the base node formatter. Often empty in standard usage.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Although the set of relationship types is usually small, response time still depends on Neo4J server performance, network latency, and the CData layer; use a higher timeout for remote or heavily loaded clusters.
- **Limitations**: The node only returns relationship type names and connector-exposed metadata; it does not provide counts of relationships, example records, or property details. Use Neo4J Relationships by Type or Neo4J Query for detailed data.
- **Behavior**: This is a read-only introspection node that issues a /relationship-types call against the configured Neo4J service and does not modify data or schema, making it safe for production environments.
- **Behavior**: Errors such as invalid credentials, unreachable hosts, or server-side failures are propagated through the text and JSON outputs as error messages; the node does not automatically retry failed calls.

## Troubleshooting
- **Authentication or connection errors**: If the text or JSON output reports authentication failure or cannot connect, verify that credentials_path points to a valid neo4j credential configuration and that the Neo4J instance is reachable from the Salt runtime environment.
- **Operation timed out**: When you see timeout-related errors, increase the timeout input and confirm that the Neo4J server and CData integration are healthy. For slower environments, values in the 60–120 second range may be appropriate.
- **Unexpectedly empty result set**: If no relationship types are returned but you expect some, ensure that the credentials connect to the correct database and that the user has sufficient privileges for schema introspection on that database.
- **Downstream JSON parsing failures**: If another node fails while parsing this node’s json output, log and inspect the raw JSON string to confirm the actual key structure used by your CData deployment, then adjust downstream parsing or mapping to match.
