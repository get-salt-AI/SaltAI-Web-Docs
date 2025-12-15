# Neo4J Write

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes write (CREATE/UPDATE/DELETE) Cypher statements against a Neo4J database. Supports parameterized queries via a JSON parameters object and returns formatted text plus raw JSON results. Uses stored credentials to connect and enforces a configurable timeout.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jwrite.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to modify data in a Neo4J graph (e.g., creating nodes/relationships, updating properties, deleting entities). Provide a Cypher write statement and optional parameters in JSON. It’s typically placed after a credentials setup step and before any downstream steps that rely on the updated graph state.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the saved Neo4J credentials to authenticate requests.</td><td style="word-wrap: break-word;">/workspace/credentials/neo4j.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the write operation before failing.</td><td style="word-wrap: break-word;">120</td></tr>
<tr><td style="word-wrap: break-word;">cypher_query</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Cypher write query to execute (e.g., CREATE, MERGE, SET, DELETE, REMOVE).</td><td style="word-wrap: break-word;">CREATE (n:Person {name: $name, age: $age})</td></tr>
<tr><td style="word-wrap: break-word;">parameters</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing parameter key-value pairs used in the Cypher query.</td><td style="word-wrap: break-word;">{"name": "John", "age": 30}</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the write operation result.</td><td style="word-wrap: break-word;">Neo4J Write Results Records affected: 1</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON response from the write execution, including any returned metadata.</td><td style="word-wrap: break-word;">{"summary": {"counters": {"nodesCreated": 1, "propertiesSet": 2}}}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML-rendered representation of results. Empty by default for this node.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Excel output of results. Empty by default for this node.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF output of results. Empty by default for this node.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Write operations modify data**: Ensure you have appropriate permissions and test on non-production environments when possible.
- **Parameters must be valid JSON**: Invalid JSON falls back to an empty parameter set, which may lead to unexpected query behavior.
- **Timeout applies to the full request**: Long-running operations may fail if they exceed the configured timeout.
- **Credentials required**: The provided credentials must have sufficient privileges to perform write operations.
- **Parameterized queries recommended**: Use parameters to avoid injection risks and improve performance through query planning.
- **Result format**: Text and JSON are populated by default; HTML, XLSX, and PDF outputs are left empty unless enhanced by downstream nodes.

## Troubleshooting
- **Invalid JSON in parameters**: Ensure the 'parameters' field is a valid JSON string (e.g., use double quotes and proper escaping).
- **Authentication or permission errors**: Verify the credentials file path and that the user has write privileges in Neo4J.
- **Cypher syntax errors**: Validate your Cypher write statement using a Neo4J client or playground; correct any syntax issues.
- **Operation times out**: Increase the 'timeout' value or optimize the query and database performance.
- **No changes applied**: Check that your query targets existing nodes/relationships (for MATCH/SET/DELETE) or that MERGE conditions are correct.
- **Parameter name mismatches**: Ensure the parameter keys in the JSON match the $placeholders used in the Cypher query.
