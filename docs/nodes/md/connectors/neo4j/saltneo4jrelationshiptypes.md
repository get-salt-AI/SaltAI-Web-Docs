# Neo4J Relationship Types

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves all relationship types defined in a Neo4J database. It connects using a provided database URI, calls the Neo4J data connector to fetch types, and returns both a human-readable summary and the raw JSON.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jrelationshiptypes.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to inspect or discover the available relationship types in a Neo4J graph before building queries, schema exploration, or analytics. Typically placed early in a workflow to inform downstream nodes (e.g., querying relationships by type).

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Neo4J connection URI. Must include protocol, host, optional port, and optional database (e.g., bolt, bolt+s, neo4j, neo4j+s).</td><td style="word-wrap: break-word;">bolt://neo4j:<password>@localhost:7687?database=neo4j</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Request timeout in seconds for the data connector call.</td><td style="word-wrap: break-word;">60</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the retrieved relationship types.</td><td style="word-wrap: break-word;">Neo4J Relationship Types: KNOWS, LIKES, WORKS_WITH</td></tr>
<tr><td style="word-wrap: break-word;">json_result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON string of the relationship types returned by the service.</td><td style="word-wrap: break-word;">["KNOWS", "LIKES", "WORKS_WITH"]</td></tr>
<tr><td style="word-wrap: break-word;">html_table</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML table representation (empty when using default text output).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded XLSX data (empty when using default text output).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf_data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base64-encoded PDF data (empty when using default text output).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials URI required**: The credentials_path must be a valid Neo4J-style URI (e.g., bolt://, bolt+s://, neo4j://, neo4j+s://).
- **Service endpoint dependency**: This node calls the Neo4J data connector service; ensure the 'neo4j_data_connector' endpoint is configured in ENDPOINTS.
- **Output formats**: By default, only text and JSON outputs are populated. HTML/XLSX/PDF outputs are empty unless a node explicitly generates those formats.
- **Scope of results**: Returned relationship types reflect what's present in the target database and may change as the graph evolves.

## Troubleshooting
- **Invalid credentials URI**: If you see an error about an invalid credentials URI, verify the scheme, host, port, and query parameters in credentials_path.
- **Connection or auth failures**: If the request fails, confirm the service endpoint is reachable, the database is running, and the username/password in the URI are correct.
- **Empty results**: If no relationship types are returned, ensure your database contains relationships or check that you are connecting to the intended database.
- **Timeouts**: Increase the timeout input if the database or network is slow.
