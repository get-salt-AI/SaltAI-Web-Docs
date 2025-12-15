# Neo4J Property Keys

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves all distinct property keys present in the connected Neo4J database. It loads saved Neo4J credentials, calls the backend service to fetch keys, and returns a human-readable summary along with the raw JSON data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jpropertykeys.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need an overview of all property keys used across nodes and relationships in your Neo4J graph. It’s typically placed after a credentials setup step and before query-building or schema analysis tasks to inform consistent property naming and validation.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">CREDENTIALS</td><td style="word-wrap: break-word;">Path or reference to saved Neo4J credentials configured for the service.</td><td style="word-wrap: break-word;">/configs/credentials/neo4j.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the operation before failing.</td><td style="word-wrap: break-word;">120</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Readable summary of the retrieved property keys.</td><td style="word-wrap: break-word;">Neo4J Property Keys Total keys: 24 Sample: ['name', 'age', 'createdAt', 'email', 'title']</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-encoded result data containing the property keys and related metadata, if any.</td><td style="word-wrap: break-word;">{"keys": ["name", "age", "createdAt", "email", "title"]}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML representation (not used by this node’s default output; returned empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">XLSX content or reference (not used by this node’s default output; returned empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">PDF content or reference (not used by this node’s default output; returned empty).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Credentials required**: Ensure a valid Neo4J credential set is available at the provided credentials_path.
- **Read-only operation**: This node only reads metadata (property keys) and does not modify the database.
- **Timeout behavior**: Large graphs or slow connections may require increasing the timeout value.
- **Output structure**: The default outputs include a text summary and a JSON string; other formats are returned empty by design.

## Troubleshooting
- **Authentication errors**: Verify the credentials_path points to a valid Neo4J configuration and that the username/password are correct.
- **Network/connection issues**: If requests time out or fail to connect, check network access, host/port, and increase the timeout as needed.
- **Empty results**: If the JSON payload contains no keys, confirm that your database contains nodes/relationships with properties.
- **Invalid credentials path**: Ensure the path is accessible to the runtime environment and formatted correctly.
