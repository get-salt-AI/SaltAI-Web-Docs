# Neo4J Graph Explorer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Interactive exploration node for Neo4J graphs. It can inspect schema (labels, relationship types, constraints/indexes), find shortest paths between two nodes up to a depth limit, and run basic graph analytics (PageRank centrality and label propagation community detection). Results can be returned as text/JSON and optionally as HTML, XLSX, or PDF for easy sharing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jgraphexplorer.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to quickly understand or analyze the structure and connectivity of your Neo4J database. Typical workflow: provide valid Neo4J credentials, choose an action (schema exploration, path finding, centrality, or community detection), set optional parameters (start/end nodes, max depth), and select an output format to view or export results.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path to the stored Neo4J credentials for the service.</td><td style="word-wrap: break-word;"><path-to-credentials>/neo4j.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (seconds) to wait for the exploration or analytics request to complete.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">action</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select the operation to perform: explore_schema (schema overview), find_paths (paths between nodes), analyze_centrality (PageRank), or community_detection (label propagation).</td><td style="word-wrap: break-word;">explore_schema</td></tr>
<tr><td style="word-wrap: break-word;">start_node</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Start node reference for path finding. Interpreted by name property; you can pass either a plain name or a label-prefixed value like 'Person:John'. Used only when action is find_paths.</td><td style="word-wrap: break-word;">Person:John</td></tr>
<tr><td style="word-wrap: break-word;">end_node</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">End node reference for path finding. Interpreted by name property; you can pass either a plain name or a label-prefixed value like 'Person:Jane'. Used only when action is find_paths.</td><td style="word-wrap: break-word;">Person:Jane</td></tr>
<tr><td style="word-wrap: break-word;">max_depth</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum traversal depth for path finding (minimum 1, maximum 10). Used only when action is find_paths.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">output_format</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select the output format: text (plain text and JSON), html (HTML table), xlsx (Excel), pdf (PDF), or all (returns all formats).</td><td style="word-wrap: break-word;">text</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary or title of the results. Present when output_format is text or all.</td><td style="word-wrap: break-word;">Neo4J Graph Schema Exploration</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw results in JSON string form, suitable for downstream processing.</td><td style="word-wrap: break-word;">{"schema_info": {...}, "node_labels": [...], "relationship_types": [...]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML table representation of the results. Present when output_format is html or all.</td><td style="word-wrap: break-word;"><table>...</table></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Binary data for an Excel export of the results. Present when output_format is xlsx or all.</td><td style="word-wrap: break-word;"><binary-xlsx-bytes></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">Binary data for a PDF export of the results. Present when output_format is pdf or all.</td><td style="word-wrap: break-word;"><binary-pdf-bytes></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: You must provide a valid credentials_path for a Neo4J connection.
- **Path finding requirements**: start_node and end_node are required for find_paths. Matching is done by the 'name' property of nodes.
- **Depth limit**: max_depth is constrained between 1 and 10.
- **Graph Data Science dependency**: analyze_centrality and community_detection use GDS algorithms (PageRank and Label Propagation) on a projected graph named 'myGraph'. Ensure GDS is installed and 'myGraph' exists.
- **Result limits**: Path finding returns up to 10 shortest paths; analytics list up to the top 20 items.
- **Output formats**: 'text' returns text + JSON; 'html', 'xlsx', or 'pdf' return only that format plus JSON; 'all' returns all formats.

## Troubleshooting
- **No paths found**: Verify that start_node and end_node match node 'name' properties and increase max_depth if necessary.
- **GDS errors**: Ensure the Neo4J Graph Data Science library is installed and a graph projection named 'myGraph' exists with proper configuration.
- **Permission/connection issues**: Check credentials_path content, network access to the Neo4J server, and that the database is reachable within the timeout.
- **Timeouts or large graph issues**: Increase timeout or narrow the scope (reduce max_depth or dataset size).
- **Empty outputs in selected format**: Use output_format='all' to retrieve all formats and confirm data presence; if still empty, inspect the JSON output for error messages.
