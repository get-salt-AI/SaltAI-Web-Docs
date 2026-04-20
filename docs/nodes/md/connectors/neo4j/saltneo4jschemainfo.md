# Neo4J Schema Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node connects to a Neo4J instance using configured credentials and fetches schema-level information, specifically constraints and indexes. It calls the Neo4J service endpoint "/schema-info" via the shared database base node and returns the result in a standard Salt text-plus-JSON format. Use it to inspect and document how your graph is structured at the schema level.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jschemainfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Neo4J Schema Info node when you need an up-to-date view of your Neo4J database schema, including indexes and constraints, without writing Cypher manually. It is typically placed in pipelines focused on auditing, documenting, or tuning a Neo4J graph: upstream, you supply a `credentials_path` from a credentials/config node; downstream, you can feed the JSON output into reporting, visualization, or validation nodes (for example, to build schema reports, compare environments, or check required constraints). Common scenarios include performance tuning (verifying indexes exist before optimizing queries), data governance (ensuring uniqueness and existence constraints are present), and environment documentation (exporting all constraints and indexes for reference). It sits in the Neo4J category alongside nodes such as `SaltNeo4JNodeLabels`, `SaltNeo4JRelationshipTypes`, and `SaltNeo4JDatabaseInfo`; a typical pattern is: obtain credentials → run `SaltNeo4JTestConnection` → run `SaltNeo4JSchemaInfo` and other introspection nodes → pass the results into analytics or reporting stages.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to a stored Neo4J credential set that uses the "neo4j" credential template. This must resolve to connection details (URI, username, password, and related options) that the base database node can use to contact the Neo4J service.</td><td style="word-wrap: break-word;">/workspace/credentials/neo4j/prod-graph.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the `/schema-info` request before failing. Larger schemas or slower connections may require a higher timeout; too small a value can cause otherwise healthy requests to be terminated early.</td><td style="word-wrap: break-word;">60</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the Neo4J schema information, labeled as "Neo4J Schema Info". Typically includes a formatted list or description of constraints and indexes so that users can quickly inspect the schema without parsing JSON.</td><td style="word-wrap: break-word;">Neo4J Schema Info  Constraints: - CONSTRAINT ON ( n:Person ) ASSERT n.id IS UNIQUE  Indexes: - INDEX ON :Order(orderDate) </td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded string containing the raw schema metadata returned from the `/schema-info` endpoint. This usually includes arrays or objects describing constraints and indexes and is intended for programmatic use by downstream nodes.</td><td style="word-wrap: break-word;">{"constraints":[{"name":"person_id_unique","type":"UNIQUENESS","labelsOrTypes":["Person"],"properties":["id"]}],"indexes":[{"name":"order_orderDate_index","entityType":"NODE","labelsOrTypes":["Order"],"properties":["orderDate"],"type":"BTREE"}]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation of the schema information if produced by the base node’s formatter, often as lists or tables for direct embedding into dashboards or HTML reports. This may be an empty string when HTML formatting is not generated.</td><td style="word-wrap: break-word;"><h2>Neo4J Schema Info</h2><h3>Constraints</h3><ul><li>CONSTRAINT ON ( n:Person ) ASSERT n.id IS UNIQUE</li></ul><h3>Indexes</h3><ul><li>INDEX ON :Order(orderDate)</li></ul></td></tr>
<tr><td style="word-wrap: break-word;">file</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or identifier to an exported file (for example CSV, XLSX, or PDF) containing the schema information, if the base node’s export functionality is enabled for this operation. When no file is created, this will typically be an empty string.</td><td style="word-wrap: break-word;">/workspace/exports/neo4j_schema_info_2024-06-01.xlsx</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded auxiliary metadata about the request or result, such as timestamps or service diagnostics. This field may be empty when no additional metadata is provided.</td><td style="word-wrap: break-word;">{"generated_at":"2024-06-01T12:34:56Z","service":"neo4j","endpoint":"/schema-info"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: On databases with many indexes and constraints, the `/schema-info` operation can take noticeable time; increase `timeout` for large production graphs or high-latency networks.
- **Limitations**: The node returns only schema-level metadata (constraints and indexes). It does not list node labels, relationship types, or example data; use `SaltNeo4JNodeLabels` and `SaltNeo4JRelationshipTypes` for those.
- **Behavior**: Credentials are reloaded from `credentials_path` on each run, so changes to the credential file or reference take effect immediately without restarting pipelines.
- **Behavior**: The internal structure of the `json` payload is dictated by the Neo4J backend’s `/schema-info` implementation and may evolve. Downstream logic should validate fields before relying on specific keys.

## Troubleshooting
- **Authentication or connection errors**: If the output indicates connection failures or authentication issues (for example HTTP 401/403 or messages about being unable to reach Neo4J), confirm that `credentials_path` points to a valid credential file, that the URI and login details are correct, and validate them first with the `SaltNeo4JTestConnection` node.
- **Timeouts on large schemas**: When timeouts occur or responses are incomplete, the `timeout` value may be too low relative to schema size or server load. Increase `timeout` (for example from 30 to 90 seconds) and retry, and consider running during periods of lower database activity.
- **Unexpectedly empty schema information**: If you expect constraints or indexes but none are reported, verify directly in Neo4J (for example using `SHOW CONSTRAINTS` and `SHOW INDEXES`) that they exist on the database targeted by your credentials, and confirm that your credentials are pointing at the correct database in multi-database setups.
- **Downstream JSON parsing failures**: If nodes consuming the `json` output raise parsing or key errors, first inspect the `json` string with a generic JSON viewer node to confirm the actual structure. Update downstream logic to handle the current field names and add checks for missing or optional sections.
