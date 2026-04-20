# Neo4J Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node executes a Cypher query on a Neo4J database using configured credentials and optional JSON parameters. It parses the provided `parameters` string into JSON, calls a Neo4J-backed service endpoint, and formats the response using a shared database base node into both human-readable text and structured JSON. It is designed for query/read-style interactions and standardizes connection handling and result formatting across graph-related workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to query data from a Neo4J graph database as part of a Salt pipeline. Typical scenarios include fetching user-item relationships, exploring entity neighborhoods, and extracting graph features for analytics or LLM reasoning. Place it after a credentials/configuration component that provides the Neo4J connection and before nodes that consume or transform the query results, such as JSON processing, visualization, or language model nodes. Upstream nodes can generate the `cypher_query` dynamically (for example, from templates or prompt-based logic) and build the `parameters` JSON from user input or earlier outputs. Downstream, you might parse the JSON into a tabular form, compute graph metrics, or feed the text summary to an LLM for explanation. For reliability, test your queries directly in a Neo4J interface first, keep queries parameterized instead of concatenating strings, and limit result size to ensure good performance.

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
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of seconds the node waits for the Neo4J query to complete before aborting. This protects workflows from hanging due to complex or inefficient queries. If omitted, a default timeout defined by the base node or environment is applied.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">cypher_query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Cypher query string executed against the Neo4J database. It should use valid Cypher syntax and is primarily intended for read operations (MATCH, OPTIONAL MATCH, RETURN). You can include parameter placeholders such as $userId or $limit, which are populated from the `parameters` JSON input.</td><td style="word-wrap: break-word;">MATCH (u:User {id: $userId})-[:PURCHASED]->(p:Product) RETURN p, p.category AS category ORDER BY p.popularity DESC LIMIT $limit</td></tr>
<tr><td style="word-wrap: break-word;">parameters</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string containing key-value pairs for Cypher parameters. Keys should match the parameter names referenced in the query without the "$" prefix, and values should be JSON-serializable types supported by Neo4J. If left blank or containing only whitespace, the node uses an empty parameter object. If parsing fails due to invalid JSON, the node logs a warning and also falls back to an empty object.</td><td style="word-wrap: break-word;">{"userId": "user_789", "limit": 10}</td></tr>
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
<tr><td style="word-wrap: break-word;">connection_info</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">String describing the Neo4J connection or environment used to run the query, as produced by the shared base node. This can be used for auditing, logging, or routing related operations to the same data source.</td><td style="word-wrap: break-word;">neo4j connection: bolt+s://graph-prod.internal:7687 (database=neo4j)</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-serialized string containing the structured query results. It typically includes an array of records with nodes, relationships, and their properties, along with any summary metadata. This is the primary output for downstream programmatic processing and data transformation.</td><td style="word-wrap: break-word;">{"records": [{"p": {"labels": ["Product"], "properties": {"sku": "SKU-555", "name": "4K Monitor", "category": "Electronics", "popularity": 0.91}}}], "summary": {"row_count": 1, "query_type": "read"}}</td></tr>
<tr><td style="word-wrap: break-word;">text_summary</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable text summary of the Neo4J query results generated by the database base node. Suitable for direct display in UIs or as input to language model nodes that will interpret or narrate the results.</td><td style="word-wrap: break-word;">Neo4J Query Results 1 record returned. Top purchased product for user_789: 4K Monitor (SKU-555), category Electronics, popularity 0.91.</td></tr>
<tr><td style="word-wrap: break-word;">aux_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Additional metadata or diagnostic information about the query execution, serialized as a string (often JSON) or left empty. It may include timing, database statistics, or internal identifiers useful for debugging and optimization.</td><td style="word-wrap: break-word;">{"execution_stats": {"elapsed_ms": 22, "db_hits": 81}}</td></tr>
<tr><td style="word-wrap: break-word;">raw_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw or minimally processed response payload from the underlying Neo4J service, serialized as a string. This is mainly useful for debugging or advanced workflows that require access to the exact service response beyond the formatted JSON.</td><td style="word-wrap: break-word;">{"raw": {"results": [{"columns": ["p"], "data": [...] }], "stats": {"contains_updates": false}}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Use LIMIT and selective WHERE clauses in your Cypher queries to keep result sets small and avoid long-running operations that risk hitting the timeout.
- **Limitations**: If the `parameters` string is invalid JSON, the node logs a warning and silently falls back to an empty parameter object, which may cause filters relying on parameters to be ignored or queries to fail.
- **Behavior**: This node is optimized for read/query workloads. For mutating operations such as creating or updating nodes and relationships, use the dedicated write node (SaltNeo4JWrite) instead for better semantics and separation of concerns.
- **Behavior**: Parameter names are case-sensitive and must exactly match between the Cypher query (for example, $userId) and the keys in the `parameters` JSON ("userId"); mismatches typically result in Neo4J errors about missing parameters.

## Troubleshooting
- **Parameters seem ignored**: Check the `parameters` input for JSON syntax errors. When parsing fails, the node falls back to `{}` and issues a warning in logs; correct the JSON and ensure that each parameter used in the query has a corresponding key.
- **Neo4J returns syntax or parameter errors**: Errors like "Invalid input" or messages about missing parameters indicate issues in `cypher_query` or mismatch between parameter placeholders and JSON keys. Validate the query in a Neo4J browser, confirm parameter names, and then update the node.
- **Queries are slow or time out**: If queries frequently hit the timeout, profile and optimize them in Neo4J, add appropriate indexes, reduce the result size with LIMIT, and refine WHERE clauses. Only increase the `timeout` after optimizing the query.
- **Connection or authentication issues**: Errors indicating authentication failures or unreachable hosts usually mean `credentials_path` points to a misconfigured or missing credential. Verify the stored Neo4J configuration (URI, username, password or token) and ensure the database is reachable from the execution environment.
