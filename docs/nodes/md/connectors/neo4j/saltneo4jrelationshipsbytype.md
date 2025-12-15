# Neo4J Relationships by Type

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Fetches relationships from a Neo4J database filtered by a specific relationship type, with an optional limit on the number of results. It handles credential loading and remote service requests, returning a human-readable summary and the raw JSON payload.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jrelationshipsbytype.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to inspect or export relationships of a specific type from your Neo4J graph (e.g., KNOWS, WORKS_WITH). Typical workflow: provide a valid Neo4J credential path, choose the relationship type, set a result limit, and connect the outputs to viewers or downstream processing nodes.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or reference to the stored Neo4J credentials to authenticate against the database.</td><td style="word-wrap: break-word;"><path-to-neo4j-credentials></td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before it times out.</td><td style="word-wrap: break-word;">60</td></tr>
<tr><td style="word-wrap: break-word;">relationship_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Neo4J relationship type to filter by.</td><td style="word-wrap: break-word;">KNOWS</td></tr>
<tr><td style="word-wrap: break-word;">limit</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of relationships to return.</td><td style="word-wrap: break-word;">100</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A formatted text summary of the query results for quick inspection.</td><td style="word-wrap: break-word;">Neo4J Relationships by Type: KNOWS — 100 relationships returned.</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw JSON result containing the list of relationships and related metadata.</td><td style="word-wrap: break-word;">{"relationships": [{"start":"123","end":"456","type":"KNOWS","properties":{}}], "count": 100}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: You must supply valid Neo4J credentials via credentials_path.
- **Relationship type must exist**: Ensure the relationship_type matches an existing type in your database.
- **Result limiting**: limit must be an integer between 1 and 10000.
- **Timeouts**: Large graphs or complex filtering may require increasing the timeout value.
- **Output formats**: The node returns a human-readable text summary and a JSON string suitable for parsing or export.

## Troubleshooting
- **Empty results**: If you receive no relationships, verify the relationship_type spelling and confirm such relationships exist.
- **Invalid JSON downstream**: Ensure downstream nodes expecting structured data parse the JSON output field, not the text summary.
- **Authentication failures**: Double-check the credentials_path points to a valid Neo4J credential and that access is permitted.
- **Timeout errors**: Increase the timeout input value or reduce the limit to improve responsiveness.
