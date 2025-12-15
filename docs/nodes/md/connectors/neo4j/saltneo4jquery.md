# Neo4J Query

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes a Cypher query against a Neo4J database and returns both a human-readable summary and the raw JSON results. It accepts optional query parameters in JSON format and uses stored credentials to connect.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jquery.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to read or analyze graph data from a Neo4J instance using Cypher. Typical workflow: provide a credentials file, set a timeout, write your Cypher query, optionally supply parameters as JSON, and connect the outputs to downstream nodes for analysis or display.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Path or reference to the saved credentials for the Neo4J service. Must match the 'neo4j' credential template.</td><td style="word-wrap: break-word;"><path-to-neo4j-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">Not specified</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the query to complete before timing out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">cypher_query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Cypher query to execute.</td><td style="word-wrap: break-word;">MATCH (p:Person)-[r:KNOWS]->(q:Person) RETURN p.name, q.name LIMIT 25</td></tr>
<tr><td style="word-wrap: break-word;">parameters</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON object containing named parameters for the Cypher query.</td><td style="word-wrap: break-word;">{"name": "Alice", "limit": 10}</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary or status message for the executed query.</td><td style="word-wrap: break-word;">Neo4J Query Results</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON payload returned from the service containing query results.</td><td style="word-wrap: break-word;">{"results": [{"p.name": "Alice", "q.name": "Bob"}]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Parameters must be valid JSON**: If the parameters string is not valid JSON, the node logs a warning and proceeds with an empty parameter object.
- **Credentials required**: The node uses the 'neo4j' credential template; ensure your credentials file matches this template.
- **Timeout behavior**: Long-running queries may be interrupted if they exceed the specified timeout.
- **Read-only behavior**: This node is intended for read (query) operations. Use a separate write node for creating or modifying graph data.
- **Category**: Listed under the 'Neo4J' category in the Salt menu.

## Troubleshooting
- **Invalid JSON in parameters**: Ensure 'parameters' is a properly formatted JSON string (e.g., '{"key": "value"}'). If invalid, the node will run with empty parameters.
- **Connection failures**: Verify the credentials_path points to a valid Neo4J credential set and that the database is reachable from your environment.
- **Query errors**: Syntax or runtime errors in the Cypher query will cause the request to fail. Test the query in a Cypher console and correct any issues.
- **Timeouts**: Increase 'timeout' for complex queries or optimize the Cypher query to reduce execution time.
