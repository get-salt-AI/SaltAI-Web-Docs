# Neo4J Nodes by Label

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves nodes from a Neo4J database filtered by a specified label, with a configurable maximum number of results. It returns a human-readable text summary along with machine-readable JSON, and may also provide empty placeholders for additional export formats.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jnodesbylabel.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to quickly browse or extract entities of a specific type (label) from your Neo4J graph, e.g., all Person nodes. Typical workflow: provide valid Neo4J credentials, set the label (such as Person, Product, etc.), choose a result limit, and connect the outputs to downstream nodes for analysis, visualization, or export.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the stored credentials for the Neo4J service, following the 'neo4j' credential template.</td><td style="word-wrap: break-word;"><path-to-neo4j-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before it times out.</td><td style="word-wrap: break-word;">120</td></tr>
<tr><td style="word-wrap: break-word;">label</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Neo4J node label to filter by.</td><td style="word-wrap: break-word;">Person</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of nodes to return. Supported range is 1 to 10000.</td><td style="word-wrap: break-word;">100</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">TEXT</td><td style="word-wrap: break-word;">A readable summary of the retrieved nodes and operation context.</td><td style="word-wrap: break-word;">Neo4J Nodes by Label: Person Returned 100 nodes.</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw JSON data of the retrieved nodes and related metadata.</td><td style="word-wrap: break-word;">{"nodes": [{"id": 1, "labels": ["Person"], "properties": {"name": "Alice"}}], "count": 100}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML-rendered version of the results if available; may be empty if not generated.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">XLSX</td><td style="word-wrap: break-word;">Excel file data for the results if available; may be empty if not generated.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">PDF</td><td style="word-wrap: break-word;">PDF file data for the results if available; may be empty if not generated.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: You must provide valid Neo4J credentials using the 'neo4j' credential template.
- **Label sensitivity**: Labels must match existing labels in your graph; otherwise the result may be empty.
- **Result cap**: The limit parameter accepts values from 1 to 10000; higher values may increase execution time.
- **Timeout considerations**: Large datasets or busy databases may require increasing the timeout input to avoid timeouts.
- **Outputs**: The primary consumable outputs are text and json; html/xlsx/pdf may be empty depending on configuration.

## Troubleshooting
- **Empty results**: Verify the label is correct and exists in your database. Try a smaller limit or different label.
- **Timeouts**: Increase the timeout value or reduce the limit. Ensure network connectivity and database performance are adequate.
- **Authentication errors**: Check the credentials_path points to valid credentials and that the account has read permissions.
- **Malformed output handling**: If downstream nodes expect specific fields, inspect the json output structure and adapt accordingly.
