# Oracle Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node constructs a properly formatted Oracle database connection string using individual fields such as host, port, service name or SID, username, password, and encoding. It standardizes how Oracle credentials are represented so they can be reused by other Oracle-focused nodes, avoiding manual string-building and ensuring consistency across workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracleconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to centralize or standardize how you connect to an Oracle database before running queries or executing statements. Typical usage is near the start of a data pipeline: first define the connection parameters with SaltOracleConnectionString, then feed the resulting connection information into Oracle query, execute, or metadata nodes that expect a database URI or credentials path. Common scenarios include building a reusable Oracle connection for reporting dashboards, ETL jobs that pull from Oracle into another system, or ad-hoc analysis workflows that run multiple different queries against the same database. Upstream, the parameters are normally provided as static configuration or from credential/secret-management nodes. Downstream, nodes like "SaltOracleQuery", "SaltOracleExecute", and "SaltOracleTableInfo" will typically make use of the resulting connection info (often via a credentials file or stored configuration key created by a higher-level credential node). Best practices: (1) Choose connection_type correctly based on whether your Oracle endpoint exposes a service name or a SID; (2) Keep username and password values managed via secure credential storage nodes where possible rather than hardcoding; (3) Use a consistent encoding (usually UTF-8) across all Oracle-related nodes to avoid character set mismatches.

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
<tr><td style="word-wrap: break-word;">host</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Hostname or IP address of the Oracle database server. Must be reachable from the Salt execution environment and can be a DNS name or direct IP. Do not include protocol prefixes like http://.</td><td style="word-wrap: break-word;">oracle-db.internal.example.com</td></tr>
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">TCP port on which the Oracle listener is exposed. Must be a valid port between 1 and 65535; defaults to the standard Oracle port 1521.</td><td style="word-wrap: break-word;">1521</td></tr>
<tr><td style="word-wrap: break-word;">service_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The Oracle service name or SID used for the connection, depending on connection_type. For service-name-based connections this is typically the global database name; for SID-based connections it is the instance SID.</td><td style="word-wrap: break-word;">ORCLPDB1</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Database account username that has permission to connect and run the downstream operations. Must correspond to a valid Oracle user in the target database.</td><td style="word-wrap: break-word;">reporting_user</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Password for the specified Oracle user. This is treated as a secret value and should generally come from a secure credentials store or environment configuration rather than being hardcoded.</td><td style="word-wrap: break-word;"><oracle-db-password></td></tr>
<tr><td style="word-wrap: break-word;">connection_type</td><td>True</td><td style="word-wrap: break-word;">["service_name", "sid"]</td><td style="word-wrap: break-word;">Specifies whether the connection will be built using an Oracle service name or a SID. Must be either "service_name" (typical for modern Oracle setups) or "sid" (older or specific deployments). The internal URI format will differ depending on this choice.</td><td style="word-wrap: break-word;">service_name</td></tr>
<tr><td style="word-wrap: break-word;">encoding</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Character encoding for the Oracle connection. Usually "UTF-8". Should match the character set expectations of your database and client to avoid encoding issues.</td><td style="word-wrap: break-word;">UTF-8</td></tr>
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
<tr><td style="word-wrap: break-word;">connection_string</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A fully assembled Oracle connection URI or DSN-style string derived from the provided parameters. This string is suitable for use by Oracle/CData database nodes, internal database connectors, or any system that expects a single URI to establish the connection.</td><td style="word-wrap: break-word;">oracle://reporting_user:<oracle-db-password>@oracle-db.internal.example.com:1521/ORCLPDB1?encoding=UTF-8&connectionType=service_name</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: This node only performs string construction and basic parameter handling, so it is lightweight and adds negligible overhead even in large workflows.
- **Limitations**: The node does not test or validate actual connectivity to the Oracle server; a syntactically valid URI may still fail later if host, port, or credentials are incorrect.
- **Behavior**: The connection_type setting directly affects how the internal URI is built. Using the wrong option (service_name vs SID) can cause connection failures that appear as listener or authentication errors.
- **Behavior**: Passwords are accepted as input but this node does not itself manage secure storage or rotation. Integrate with dedicated credential/secret management nodes or configuration mechanisms to handle secrets safely.

## Troubleshooting
- **Common Error 1**: Downstream Oracle nodes report "ORA-12514: TNS:listener does not currently know of service requested" – verify that connection_type is set to "service_name" and that the service_name field exactly matches the configured service on the Oracle listener.
- **Common Error 2**: Downstream operations fail with "ORA-12505: TNS:listener does not currently know of SID given in connect descriptor" – switch connection_type to "sid" and ensure the service_name field actually contains the SID value used by the database instance.
- **Common Error 3**: Authentication errors like "ORA-01017: invalid username/password; logon denied" – confirm that username and password are correct, case sensitive, and free of trailing spaces or hidden characters, and that the account is not locked or expired.
