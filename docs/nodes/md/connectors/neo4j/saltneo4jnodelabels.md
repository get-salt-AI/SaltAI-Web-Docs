# Neo4J Node Labels

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves the list of all node labels available in a connected Neo4J database. It authenticates using the provided credentials, calls the service to fetch labels, and returns a human-readable summary along with machine-readable JSON.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jnodelabels.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover available labels in your Neo4J graph before constructing queries or building graph exploration workflows. It is commonly used early in a pipeline to inform filtering, schema exploration, or UI selection lists.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to stored Neo4J service credentials configured for the workspace.</td><td style="word-wrap: break-word;"><path-to-neo4j-credentials.json></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time (in seconds) to wait for the service request before it is aborted.</td><td style="word-wrap: break-word;">60</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Readable summary of the retrieved node labels.</td><td style="word-wrap: break-word;">Neo4J Node Labels: ["Person", "Movie", "Company"]</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Raw data payload containing the node labels.</td><td style="word-wrap: break-word;">["Person", "Movie", "Company"]</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">HTML</td><td style="word-wrap: break-word;">HTML-formatted output (may be empty for this node unless enhanced rendering is enabled).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">FILE</td><td style="word-wrap: break-word;">Generated spreadsheet file (may be empty for this node unless export is enabled).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">FILE</td><td style="word-wrap: break-word;">Generated PDF file (may be empty for this node unless export is enabled).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: Ensure valid Neo4J credentials are configured and referenced by the provided credentials_path.
- **Timeout behavior**: Long-running or slow connections may require increasing the timeout value to avoid premature failures.
- **Read-only operation**: This node only reads metadata (labels) and does not modify the database.
- **Result format**: The JSON output is typically an array of label names; downstream nodes can parse it to drive dynamic selections.

## Troubleshooting
- **Empty output**: If the JSON array is empty, verify that the database contains nodes and that your user has permissions to read schema metadata.
- **Authentication errors**: If you receive authentication or authorization errors, confirm the credentials_path points to valid Neo4J credentials and that the user has appropriate access.
- **Connection timeouts**: Increase the timeout input or check network connectivity, firewall rules, and service availability.
- **Unexpected output format**: Ensure downstream steps expect a JSON array of strings; if parsing fails, validate that the service response is not an error payload.
