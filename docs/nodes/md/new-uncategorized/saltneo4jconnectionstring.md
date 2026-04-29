# Neo4J Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node constructs a Neo4J connection string and a compatible driver configuration from individual parameters such as host, port, protocol, credentials, database, and routing options. It also validates these inputs and returns a structured summary of errors and warnings so you can verify that the connection details are sensible before use. The outputs are designed to be consumed by other Neo4J-related nodes or external services that require a URI and driver settings.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltneo4jconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to assemble a correct Neo4J connection URI and driver configuration to feed into downstream graph operations. Place it early in your workflow: provide host, port, protocol, username, password, database, and optional routing context and pooling settings, then examine the validation_result (via the json or text output) to catch misconfiguration before running queries. Its connection_string or driver_config is typically passed into nodes such as SaltNeo4JQuery, SaltNeo4JWrite, SaltNeo4JNodeLabels, SaltNeo4JRelationshipTypes, or SaltNeo4JDatabaseInfo, which perform actual graph operations. This node is especially useful when you want to centralize connection logic, support switching between direct and routing protocols (bolt vs neo4j), or tune encryption and connection pooling parameters while ensuring basic validation is applied.

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
<tr><td style="word-wrap: break-word;">host</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Neo4J hostname or IP address. Must be a non-empty string without protocol prefix (no "bolt://" or "neo4j://"). Can be a DNS name (for example, a cluster endpoint) or a numeric IP.</td><td style="word-wrap: break-word;">neo4j-prod.internal.mycompany.com</td></tr>
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">TCP port number for the Neo4J service. Must be between 1 and 65535. Common values: 7687 for Bolt-based connections, 7474 for HTTP-based endpoints (though this node focuses on Bolt-style URIs).</td><td style="word-wrap: break-word;">7687</td></tr>
<tr><td style="word-wrap: break-word;">protocol</td><td>True</td><td style="word-wrap: break-word;">ENUM[bolt,bolt+s,bolt+ssc,neo4j,neo4j+s,neo4j+ssc]</td><td style="word-wrap: break-word;">Connection protocol and security mode. "bolt" is a direct, typically unencrypted connection; "bolt+s" uses TLS with trusted CA; "bolt+ssc" allows self-signed certificates. "neo4j", "neo4j+s" and "neo4j+ssc" are cluster-aware routing protocols with the same security variants. Routing context is only applied for the neo4j* protocols.</td><td style="word-wrap: break-word;">neo4j+s</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Neo4J username for authentication. When combined with a non-empty password, it is URL-encoded and embedded in the URI as username:password@. Leaving it empty is allowed but will produce a warning because unauthenticated access is discouraged.</td><td style="word-wrap: break-word;">graph_admin</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>False</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Neo4J password for the corresponding username. If provided, it is URL-encoded into the URI; if omitted or empty, the URI will not include credentials and the validation_result will include a warning recommending secure authentication.</td><td style="word-wrap: break-word;"><neo4j-admin-password></td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target Neo4J database name. If non-empty, it is appended as a query parameter (database=<name>) to the connection string. If left blank, the server’s default database will be used.</td><td style="word-wrap: break-word;">customer_graph</td></tr>
<tr><td style="word-wrap: break-word;">routing_context</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Routing context for cluster connections, provided as a JSON string mapping keys to values. Used only when protocol is one of neo4j, neo4j+s, or neo4j+ssc; the key/value pairs are added to the URI as query parameters. If the JSON cannot be parsed, it is ignored and an internal warning is logged.</td><td style="word-wrap: break-word;">{"region": "eu-west", "role": "READ"}</td></tr>
<tr><td style="word-wrap: break-word;">max_connection_lifetime</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum lifetime of a connection in the pool, in seconds. Must be between 60 and 7200. Lower values recycle connections more frequently to avoid stale connections; higher values reduce churn but may keep problematic connections around longer.</td><td style="word-wrap: break-word;">1800</td></tr>
<tr><td style="word-wrap: break-word;">max_connection_pool_size</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum number of concurrent connections in the driver pool. Valid range is 1 to 200. Values over 100 are allowed but will generate a validation warning due to potential performance and resource implications.</td><td style="word-wrap: break-word;">50</td></tr>
<tr><td style="word-wrap: break-word;">connection_acquisition_timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for a connection from the pool before failing. Must be between 10 and 600. Increase this if you expect bursts of load and want callers to wait longer rather than fail quickly.</td><td style="word-wrap: break-word;">120</td></tr>
<tr><td style="word-wrap: break-word;">encrypted</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether the driver should use encryption. When true, encryption is enabled in the driver_config and a trust strategy is also included. When false and using non-‛+s protocols, the node will add a warning suggesting encrypted protocols for production.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">trust</td><td>True</td><td style="word-wrap: break-word;">ENUM[TRUST_ALL_CERTIFICATES,TRUST_SYSTEM_CA_SIGNED_CERTIFICATES,TRUST_DEFAULT]</td><td style="word-wrap: break-word;">Trust strategy for TLS certificates when encryption is enabled. TRUST_SYSTEM_CA_SIGNED_CERTIFICATES is recommended for production; TRUST_ALL_CERTIFICATES is mainly for development or controlled environments with self-signed certificates; TRUST_DEFAULT delegates to the driver’s default behavior.</td><td style="word-wrap: break-word;">TRUST_SYSTEM_CA_SIGNED_CERTIFICATES</td></tr>
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
<tr><td style="word-wrap: break-word;">connection_string</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The constructed Neo4J connection URI including protocol, optional URL-encoded credentials, host, port, optional routing parameters, and optional database query parameter. This is the primary value to supply to Neo4J clients or nodes that accept a URI.</td><td style="word-wrap: break-word;">neo4j+s://graph_admin:%21S3cureP%40ss%23@neo4j-prod.internal.mycompany.com:7687?region=eu-west&role=READ&database=customer_graph</td></tr>
<tr><td style="word-wrap: break-word;">json</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-encoded object containing three keys: connection_string (the URI), driver_config (a dictionary of driver settings including uri, auth tuple, pooling and encryption flags, and optional trust), and validation_result (with valid, errors, and warnings). Use this when you need structured access to both configuration and validation details.</td><td style="word-wrap: break-word;">{"connection_string": "neo4j+s://graph_admin:%21S3cureP%40ss%23@neo4j-prod.internal.mycompany.com:7687?region=eu-west&role=READ&database=customer_graph", "driver_config": {"uri": "neo4j+s://graph_admin:%21S3cureP%40ss%23@neo4j-prod.internal.mycompany.com:7687?region=eu-west&role=READ&database=customer_graph", "auth": ["graph_admin", "<neo4j-admin-password>"], "max_connection_lifetime": 1800, "max_connection_pool_size": 50, "connection_acquisition_timeout": 120, "encrypted": true, "trust": "TRUST_SYSTEM_CA_SIGNED_CERTIFICATES"}, "validation_result": {"valid": true, "errors": [], "warnings": []}}</td></tr>
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A human-readable summary that prints the connection string, a pretty-printed version of the driver_config, and the validation_result section including any errors or warnings. This is useful for quick visual inspection and debugging of connection settings.</td><td style="word-wrap: break-word;">Neo4J Connection String: neo4j+s://graph_admin:%21S3cureP%40ss%23@neo4j-prod.internal.mycompany.com:7687?region=eu-west&role=READ&database=customer_graph  Driver Configuration: {"uri": "neo4j+s://graph_admin:%21S3cureP%40ss%23@neo4j-prod.internal.mycompany.com:7687?region=eu-west&role=READ&database=customer_graph", "auth": ["graph_admin", "<neo4j-admin-password>"], "max_connection_lifetime": 1800, "max_connection_pool_size": 50, "connection_acquisition_timeout": 120, "encrypted": true, "trust": "TRUST_SYSTEM_CA_SIGNED_CERTIFICATES"}  Validation Result: {"valid": true, "errors": [], "warnings": []}</td></tr>
<tr><td style="word-wrap: break-word;">reserved_1</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reserved output, currently an empty string. Present to maintain a consistent interface with related nodes.</td><td style="word-wrap: break-word;"></td></tr>
<tr><td style="word-wrap: break-word;">reserved_2</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reserved output, currently an empty string. Present to maintain a consistent interface with related nodes.</td><td style="word-wrap: break-word;"></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: High max_connection_pool_size values (greater than 100) are allowed but will trigger a warning, as very large pools can increase memory usage, connection churn, and contention under heavy load.
- **Limitations**: This node performs only basic static validation (for example, host non-empty, port range checks) and does not actually connect to Neo4J or verify credentials; use SaltNeo4JTestConnection if you need an end-to-end connectivity check.
- **Behavior**: If routing_context is not valid JSON, it is replaced with an empty dictionary and no routing parameters are appended to the URI; this does not cause the node to fail, but cluster-aware routing will not be configured via the URI.
- **Behavior**: When username or password is missing, the node omits credentials from the URI and records a warning in validation_result, but still considers the configuration valid if all other checks pass.
- **Security**: The node URL-encodes credentials in the URI; avoid logging or exposing the connection_string in unsecured channels and prefer using the structured driver_config in a controlled environment.

## Troubleshooting
- **Invalid port**: If validation_result.errors contains "Port must be between 1 and 65535", ensure that the port input is an integer within that range and that it matches your Neo4J Bolt port (commonly 7687).
- **Missing host**: If you see "Host is required" in validation_result.errors, verify that the host field is not empty or whitespace and that you have not accidentally included a protocol prefix (it should be something like neo4j-prod.internal.mycompany.com, not bolt://neo4j-prod.internal.mycompany.com).
- **Unencrypted connection warning**: If validation_result.warnings mentions "Consider using encrypted protocols (bolt+s, neo4j+s) for production", switch the protocol to an +s variant and set encrypted to true, then select an appropriate trust mode for your certificate setup.
- **No credentials warning**: If you receive the warning "Username and password are recommended for secure connections" but you intentionally rely on another authentication mechanism (for example, network-level security), you can ignore it; otherwise, provide both username and password to prevent unauthenticated access.
- **Routing not applied**: If you expect cluster-aware routing but the URI does not contain your routing parameters, confirm that protocol is one of neo4j, neo4j+s, or neo4j+ssc and that routing_context is a valid JSON string such as {"region": "eu-west"} so the parameters can be attached.
