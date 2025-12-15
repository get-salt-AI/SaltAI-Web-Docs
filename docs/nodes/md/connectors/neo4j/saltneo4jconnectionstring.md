# Neo4J Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Constructs a Neo4J connection URI from individual parameters and produces a driver configuration and validation summary. It supports bolt and neo4j protocols (including secure variants), optional routing context, database selection, and basic connection tuning options.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/neo4j/saltneo4jconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to assemble a valid Neo4J connection string and companion driver configuration to pass into downstream nodes or external services. Typical workflow: provide host, port, protocol, credentials, and optional routing/database parameters; inspect the returned JSON for the built URI, driver settings, and validation warnings/errors before using it to connect to your Neo4J instance.

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
<tr><td style="word-wrap: break-word;">host</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Neo4J hostname or IP address.</td><td style="word-wrap: break-word;">db.example.com</td></tr>
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Neo4J port number (7687 for bolt, 7474 for http).</td><td style="word-wrap: break-word;">7687</td></tr>
<tr><td style="word-wrap: break-word;">protocol</td><td>True</td><td style="word-wrap: break-word;">bolt \| bolt+s \| bolt+ssc \| neo4j \| neo4j+s \| neo4j+ssc</td><td style="word-wrap: break-word;">Connection protocol: bolt (direct), neo4j (routing); +s for encrypted, +ssc for self-signed certificates.</td><td style="word-wrap: break-word;">neo4j+s</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Neo4J username for authentication.</td><td style="word-wrap: break-word;">neo4j</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Neo4J password for authentication. Will be URL-encoded in the URI if provided.</td><td style="word-wrap: break-word;"><neo4j-password></td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional database name; if empty, the server's default database is used.</td><td style="word-wrap: break-word;">neo4j</td></tr>
<tr><td style="word-wrap: break-word;">routing_context</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string for routing context in cluster/routing setups. Applied only to neo4j/neo4j+s/neo4j+ssc protocols.</td><td style="word-wrap: break-word;">{"policy": "eu-west"}</td></tr>
<tr><td style="word-wrap: break-word;">max_connection_lifetime</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum connection lifetime in seconds.</td><td style="word-wrap: break-word;">1800</td></tr>
<tr><td style="word-wrap: break-word;">max_connection_pool_size</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of connections in the pool.</td><td style="word-wrap: break-word;">50</td></tr>
<tr><td style="word-wrap: break-word;">connection_acquisition_timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Timeout in seconds for acquiring a connection from the pool.</td><td style="word-wrap: break-word;">120</td></tr>
<tr><td style="word-wrap: break-word;">encrypted</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Enable encryption for the driver connection. Recommended for production.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">trust</td><td>True</td><td style="word-wrap: break-word;">TRUST_ALL_CERTIFICATES \| TRUST_SYSTEM_CA_SIGNED_CERTIFICATES \| TRUST_DEFAULT</td><td style="word-wrap: break-word;">Trust strategy for SSL certificates used when encryption is enabled.</td><td style="word-wrap: break-word;">TRUST_SYSTEM_CA_SIGNED_CERTIFICATES</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The generated Neo4J connection string URI.</td><td style="word-wrap: break-word;">neo4j+s://neo4j:%3Cneo4j-password%3E@db.example.com:7687?database=neo4j&policy=eu-west</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing connection_string, driver_config, and validation_result (valid/errors/warnings).</td><td style="word-wrap: break-word;">{"connection_string":"neo4j+s://neo4j:%3Cneo4j-password%3E@db.example.com:7687?database=neo4j","driver_config":{"uri":"neo4j+s://...","auth":["neo4j","<neo4j-password>"],"max_connection_lifetime":1800,"max_connection_pool_size":50,"connection_acquisition_timeout":120,"encrypted":true,"trust":"TRUST_SYSTEM_CA_SIGNED_CERTIFICATES"},"validation_result":{"valid":true,"errors":[],"warnings":["Consider using encrypted protocols (bolt+s, neo4j+s) for production"]}}</td></tr>
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML output placeholder (not used by this node; returns empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">xlsx</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">XLSX output placeholder (not used by this node; returns empty).</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">pdf</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">PDF output placeholder (not used by this node; returns empty).</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Protocols**: Use 'bolt' for direct connections and 'neo4j' for routing/cluster-aware connections. Use '+s' for encryption and '+ssc' for self-signed certificates.
- **Routing context**: Only appended for 'neo4j', 'neo4j+s', and 'neo4j+ssc' protocols; ignored for 'bolt' variants.
- **Credentials**: Username and password are optional but recommended; if set, both are URL-encoded and embedded in the URI.
- **Database parameter**: If provided, added as 'database=<name>' query parameter.
- **Validation**: The node returns a validation_result with errors (e.g., missing host, invalid port) and warnings (e.g., unencrypted protocol, large pool size).
- **Driver config**: The JSON includes 'auth', pool and timeout settings, and 'encrypted' and 'trust' when applicable for use by drivers or downstream nodes.

## Troubleshooting
- **Invalid routing_context JSON**: The node will fall back to an empty routing context and log a warning. Ensure routing_context is valid JSON.
- **Connection string missing credentials**: Provide both username and password; if either is omitted, 'auth' is set to null and a warning is returned.
- **Unencrypted protocol warning**: Switch to 'bolt+s' or 'neo4j+s' and set 'encrypted' to true for production environments.
- **Invalid port range**: Set 'port' between 1 and 65535; otherwise, 'valid' is false with an error.
- **Self-signed certificates**: If using self-signed certs, choose 'bolt+ssc' or 'neo4j+ssc' and set 'trust' as appropriate.
- **Large pool size warning**: Reduce 'max_connection_pool_size' if you see warnings or resource pressure.
