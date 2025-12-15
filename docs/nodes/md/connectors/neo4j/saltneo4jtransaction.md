# Neo4J Transaction

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Executes multiple Cypher queries as a single atomic transaction against a Neo4J database. Takes a JSON array of query objects (each with 'cypher' and 'parameters') and returns consolidated results. If any query fails, the entire transaction is rolled back.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jtransaction.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to perform multiple related updates or reads that must succeed or fail together (e.g., creating nodes and relationships, followed by verification queries). Typical workflow: provide credentials, set an appropriate timeout, compose the queries array in JSON, and connect the outputs to downstream nodes that parse text or JSON results.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored Neo4J credentials configured for the service.</td><td style="word-wrap: break-word;">/path/to/neo4j_credentials.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds allowed for the entire transaction request.</td><td style="word-wrap: break-word;">120</td></tr>
<tr><td style="word-wrap: break-word;">queries</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array of query objects. Each object must include 'cypher' (string) and 'parameters' (object) to be executed in order within a single transaction.</td><td style="word-wrap: break-word;">[{"cypher": "CREATE (n:Person {name: $name})", "parameters": {"name": "John"}}, {"cypher": "MATCH (n:Person) RETURN n", "parameters": {}}]</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the transaction results.</td><td style="word-wrap: break-word;">Neo4J Transaction Results: 2 queries executed successfully.</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON response containing detailed results for each query executed in the transaction.</td><td style="word-wrap: break-word;">{"results": [{"records": [...]}, {"records": [...]}], "stats": {...}}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML-formatted representation of the results when applicable.</td><td style="word-wrap: break-word;"><table><tr><th>name</th></tr><tr><td>John</td></tr></table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Serialized Excel content or a reference string if export is produced by the service.</td><td style="word-wrap: break-word;">xlsx_file_reference_or_content</td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Serialized PDF content or a reference string if export is produced by the service.</td><td style="word-wrap: break-word;">pdf_file_reference_or_content</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Credentials must be configured for the 'neo4j' service; the node loads them from the provided credentials_path.
- The 'queries' input must be valid JSON. If parsing fails or the string is empty, an empty list is used and no queries are executed.
- All queries are executed in order within a single transaction; any error will roll back the entire set.
- Timeout applies to the entire transaction request, not per-query.
- For write operations, ensure your user has appropriate Neo4J permissions and that any required plugins (e.g., GDS) are installed for advanced procedures.

## Troubleshooting
- Invalid JSON in 'queries': Ensure the input is a valid JSON array of objects with 'cypher' and 'parameters' keys.
- Authentication or connection errors: Verify credentials_path points to valid credentials and that the database is reachable.
- Transaction rolled back due to constraint violations or syntax errors: Test each query individually to isolate the failing statement.
- Request timeout: Increase the timeout value or simplify/optimize queries to complete within the allotted time.
- Empty or unexpected results: Confirm parameters match those referenced in Cypher queries and that target data exists.
