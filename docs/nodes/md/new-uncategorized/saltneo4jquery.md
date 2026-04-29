# Neo4J Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node runs a Cypher query against a Neo4J database using stored credentials and a configurable timeout. It accepts a Cypher query string and optional JSON parameters, sends them to the Neo4J connector service, and returns both a human-readable text summary and the raw JSON response. It is intended for flexible graph data exploration, reporting, and analysis workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltneo4jquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to read or analyze graph data from a Neo4J instance using Cypher. Typical use cases include exploring neighborhoods around key entities, building analytics on relationships, powering recommendation or search features, and extracting subgraphs for further processing by downstream nodes. In a workflow, it usually sits after a credentials or connection setup step (which provides the hidden credentials_path and timeout inputs) and before nodes that consume text or JSON, such as summarization, table-building, or visualization nodes. A common pattern is: credentials/config node -> Neo4J Query -> JSON processing or analysis node. Combine it with schema-inspection nodes like SaltNeo4JNodeLabels or SaltNeo4JRelationshipTypes to first discover labels and relationship types, then construct more precise Cypher queries. For best results, test your Cypher in a Neo4J client first, then paste it into this node, and supply parameters as valid JSON so that queries are reusable and safe.

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
<tr><td style="word-wrap: break-word;">cypher_query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Cypher query to execute. Supports any valid read-style Cypher including MATCH, OPTIONAL MATCH, UNWIND, aggregations, and RETURN clauses. Can reference named parameters using the $name syntax; these are bound from the parameters input. Multi-line queries are allowed.</td><td style="word-wrap: break-word;">MATCH (u:User {id: $userId})-[:PURCHASED]->(p:Product) RETURN p.id AS product_id, p.name AS product_name ORDER BY p.name ASC LIMIT $limit</td></tr>
<tr><td style="word-wrap: break-word;">parameters</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string representing a parameter object for the Cypher query, where keys correspond to parameter names (without the $ prefix) used in the query. Values may be strings, numbers, booleans, arrays, or nested objects as supported by the Neo4J connector. If the string is empty or not valid JSON, the node logs a warning and uses an empty parameter set instead.</td><td style="word-wrap: break-word;">{   "userId": "user_9876",   "limit": 50 }</td></tr>
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
<tr><td style="word-wrap: break-word;">text_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the Neo4J query results. This is suitable for quick inspection in the UI or logs and typically includes a short header plus a compact preview of returned records.</td><td style="word-wrap: break-word;">Neo4J Query Results: 2 records returned. 1) {"product_id": "P123", "product_name": "Noise-Cancelling Headphones"} 2) {"product_id": "P456", "product_name": "Wireless Mouse"}</td></tr>
<tr><td style="word-wrap: break-word;">json_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON response from the Neo4J query connector, serialized as a string. Usually includes an array of result objects and may include metadata like row counts. Intended for downstream nodes that parse and transform structured data.</td><td style="word-wrap: break-word;">{   "records": [     {"product_id": "P123", "product_name": "Noise-Cancelling Headphones"},     {"product_id": "P456", "product_name": "Wireless Mouse"}   ],   "summary": {     "row_count": 2,     "query": "MATCH (u:User {id: $userId})-[:PURCHASED]->(p:Product) RETURN p.id AS product_id, p.name AS product_name ORDER BY p.name ASC LIMIT $limit"   } }</td></tr>
<tr><td style="word-wrap: break-word;">extra_output_1</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reserved extra output channel provided by the shared database base node. For this node it is typically unused and returned as an empty string, but remains available for future extensions.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">extra_output_2</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Second reserved extra output channel from the base node, normally unused. Provided for consistency across database-related nodes.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Cypher queries that traverse many hops or lack appropriate indexes can be slow and may hit the timeout; consider adding indexes on frequently filtered properties and limiting traversal depth.
- **Limitations**: The parameters input must be valid JSON; if parsing fails, the node silently substitutes an empty parameter map, which can change query results without an obvious error in the output.
- **Behavior**: This node relies on shared credential loading via the database base node; if credentials_path is wrong or credentials are missing, you will see connection or authentication errors from the underlying request.
- **Behavior**: Both the human-readable summary and the raw JSON are returned as strings; any downstream structured processing must explicitly parse the json_output value.

## Troubleshooting
- **Query appears to ignore parameters**: When results look unfiltered, inspect the parameters string for JSON syntax issues such as missing quotes or trailing commas. Correct the JSON and rerun so that named parameters (like $userId) are actually bound.
- **Connection or authentication failures**: Errors indicating failure to connect, unauthorized access, or invalid credentials usually mean the credentials_path does not point to a valid Neo4J configuration. Verify the stored host, port, username, and password or token, and confirm the Neo4J service is reachable from the environment.
- **Frequent timeouts**: If queries regularly exceed the timeout, profile and simplify the Cypher query (reduce MATCH scope, add WHERE clauses, or add indexes) or increase the timeout to accommodate legitimate long-running analytics.
- **Empty or unexpected result sets**: If the node runs without error but returns no data, test the same Cypher and parameters directly in a Neo4J client. Check for mistakes in labels, relationship types, property names, or parameter values that may be over-filtering your data.
