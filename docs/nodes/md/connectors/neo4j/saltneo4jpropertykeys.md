# Neo4J Property Keys

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node queries a Neo4J-backed service endpoint to return the list of all property keys used across nodes and relationships in the target database. It leverages stored Neo4J credentials and a shared database base node to invoke the "/property-keys" operation. The response is formatted into a readable text summary plus a JSON payload that downstream nodes can parse and reuse.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jpropertykeys.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover which property keys exist in your Neo4J graph—for example during schema discovery, metadata cataloging, governance, or when building dynamic query builders and forms that must reference real property names. Place it early in a workflow after configuring and optionally testing the connection (for instance, with a "Neo4J Test Connection" node), and before more specific query or analytics nodes such as "Neo4J Query", "Neo4J Nodes by Label", or "Neo4J Graph Explorer". A common pattern is to call "Neo4J Node Labels", "Neo4J Relationship Types", and "Neo4J Property Keys" together to assemble a high-level schema overview, then feed their JSON outputs into documentation, validation, or report-generation steps.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier or path to the stored Neo4J credentials that use the "neo4j" credential template. This must resolve to a valid configuration (including connection URI and authentication details) so the backend service can connect to the intended Neo4J database.</td><td style="word-wrap: break-word;">connections/neo4j/prod-graph.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of seconds to wait for the Neo4J metadata request before aborting. Increase this value for large or heavily loaded clusters; decrease it for interactive development workflows where low latency is more important.</td><td style="word-wrap: break-word;">60</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the property keys returned by Neo4J, suitable for direct display in logs, UI panels, or conversational responses. Typically lists each key and includes a total count.</td><td style="word-wrap: break-word;">Neo4J Property Keys: - name - age - created_at - last_login - orderTotal Total keys: 5</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string containing the raw response from the "/property-keys" operation. This usually includes an array of property key names and may include additional metadata such as a total count or status fields. Downstream nodes should parse this to work programmatically with the list of available keys.</td><td style="word-wrap: break-word;">{"keys": ["name", "age", "created_at", "last_login", "orderTotal"], "count": 5}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of the property key list when provided by the base node's default formatter. Useful for embedding directly into dashboards, configuration consoles, or documentation pages without additional templating.</td><td style="word-wrap: break-word;"><h2>Neo4J Property Keys</h2><ul><li>name</li><li>age</li><li>created_at</li><li>last_login</li><li>orderTotal</li></ul></td></tr>
<tr><td style="word-wrap: break-word;">binary</td><td style="word-wrap: break-word;">BYTES</td><td style="word-wrap: break-word;">Reserved binary output channel inherited from the shared database node infrastructure. For this metadata operation it is typically empty, but it exists for compatibility with workflows that may expect or pass through binary/file data.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">extra</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Auxiliary string output that may contain additional metadata, warnings, or diagnostic information from the backend service. Often empty on success but can include non-fatal notices helpful for debugging or auditing.</td><td style="word-wrap: break-word;">{"warnings": [], "info": "Retrieved 5 property keys from neo4j://prod-graph"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: This operation is generally fast because it relies on Neo4J metadata, but on very large or heavily loaded clusters you may still need to increase the `timeout` value to avoid premature failures.
- **Limitations**: The node returns only the names of property keys, not their data types, index/constraint details, or usage statistics. For deeper schema analysis or profiling, you must run additional Cypher queries via nodes like "Neo4J Query".
- **Behavior**: The database and environment queried are determined entirely by the configuration referenced through `credentials_path`. If that path points to staging, a different cluster, or a non-default database, the property keys will reflect that target and not necessarily your primary production graph.
- **Behavior**: The exact structure of the `json` output is defined by the backend service and may evolve. Always parse it defensively (for example, checking for a `keys` array and optional metadata fields) rather than assuming a rigid, fixed schema.

## Troubleshooting
- **Connection or timeout errors**: Messages about connection failure, unreachable host, or timeouts usually indicate that `credentials_path` is misconfigured, the Neo4J instance is unreachable, or the `timeout` is too low. Verify the credentials configuration, check network access, and consider increasing the timeout.
- **Authentication or permission denied**: Errors about invalid credentials or insufficient privileges suggest the stored credentials cannot access database metadata. Update the credentials referenced by `credentials_path` so they use a user with at least read access to the database and its schema.
- **Empty or unexpectedly small key list**: If the node returns no or very few property keys but you expect many, confirm you are connected to the correct database (check the database name or default database in your credentials) and that the database actually contains nodes or relationships with properties.
- **Downstream JSON parsing failures**: If nodes that consume the `json` output fail to parse it, log or inspect the raw string to see the real structure returned. Adjust downstream parsing logic to match the actual field names and layout instead of assuming a specific schema.
