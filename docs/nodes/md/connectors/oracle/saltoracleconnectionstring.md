# Oracle Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node constructs a properly formatted Oracle database connection string from discrete parameters such as host, port, service name or SID, username, password, and encoding. It supports both service name–based and SID-based connection styles via the connection_type selector. The resulting URI can be passed directly to other Oracle-related nodes or any component that accepts an Oracle connection string.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracleconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need a reusable Oracle connection URI instead of manually typing or hard-coding one. Typical workflows place it early in a database pipeline: first configure basic connection details (host, port, service name or SID), supply credentials, choose connection_type, and optionally adjust encoding; then feed the resulting connection string into downstream Oracle operation nodes such as SaltOracleQuery, SaltOracleExecute, SaltOracleListSchemas, or SaltOracleDatabaseInfo. This is especially useful when orchestrating multiple database actions that should share the same connection, or when you want environment-specific details (dev/stage/prod) controlled at a single point. Combine it with credential-loading or environment-configuration nodes upstream, and with query/DDL/metadata nodes downstream. Best practice is to keep the password managed via Salt’s secure credential handling and only pass the constructed URI to nodes that actually need to establish a database session.

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
<tr><td style="word-wrap: break-word;">host</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Hostname or IP address of the Oracle database server. Must be reachable from the runtime environment; use DNS name or IPv4/IPv6 literal.</td><td style="word-wrap: break-word;">db-oracle-prod.internal.company.com</td></tr>
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">TCP port on which the Oracle listener is exposed. Must be between 1 and 65535; 1521 is the conventional default.</td><td style="word-wrap: break-word;">1521</td></tr>
<tr><td style="word-wrap: break-word;">service_name</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Oracle service name or SID, depending on connection_type. For connection_type="service_name" supply the service name; for "sid" supply the SID string.</td><td style="word-wrap: break-word;">ORCLPDB1</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Oracle database user to authenticate as. The user must exist and have the privileges required by downstream operations.</td><td style="word-wrap: break-word;">reporting_user</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Password for the specified Oracle user. Handled as a sensitive value; do not hard-code secrets in workflows.</td><td style="word-wrap: break-word;"><oracle-db-password></td></tr>
<tr><td style="word-wrap: break-word;">connection_type</td><td>True</td><td style="word-wrap: break-word;">["service_name", "sid"]</td><td style="word-wrap: break-word;">Chooses whether the connection string targets an Oracle service name or a SID. Use "service_name" for modern configurations, "sid" for legacy/SID-based setups.</td><td style="word-wrap: break-word;">service_name</td></tr>
<tr><td style="word-wrap: break-word;">encoding</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional character encoding to include in the connection settings. Typically "UTF-8"; change only if your Oracle instance uses a different charset.</td><td style="word-wrap: break-word;">UTF-8</td></tr>
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
<tr><td style="word-wrap: break-word;">connection_string</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The fully constructed Oracle database connection URI built from the provided parameters. This string can be fed directly into other Oracle nodes or any client library configuration input that accepts an Oracle connection string.</td><td style="word-wrap: break-word;">User Id=reporting_user;Password=<oracle-db-password>;Server=db-oracle-prod.internal.company.com;Port=1521;Service Name=ORCLPDB1;Charset=UTF-8;</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node only formats a string and performs no network I/O, so it is effectively instantaneous and adds negligible overhead to workflows.
- **Limitations**: It assumes a standard Oracle connection style and may not cover highly customized listener configurations, proxy users, or advanced security options (e.g., wallets, SSL certificates) out of the box.
- **Behavior**: The meaning of the service_name field changes with connection_type; using a SID value while connection_type is set to "service_name" (or vice versa) will yield a syntactically valid but non-working URI.
- **Behavior**: Passwords are treated as sensitive inputs; avoid logging or exposing the produced connection string in debug outputs if it embeds credentials in plain text.

## Troubleshooting
- **Connection refused or timeout**: If downstream Oracle nodes report connection failures, verify host and port reachability from the Salt runtime environment and ensure the listener is running on the specified port.
- **ORA-12514 or service not registered**: This usually indicates an incorrect service_name or SID; double-check the value against the Oracle instance configuration and ensure connection_type matches how your database is configured.
- **Authentication failed (ORA-01017)**: Confirm username and password are correct and that the user is allowed to connect to the specified service/SID; update the password input if credentials have recently changed.
- **Encoding issues (garbled characters)**: If you see corrupted text when reading non-ASCII data via downstream nodes, verify that encoding matches your Oracle database character set (for example, use "AL32UTF8" instead of plain "UTF-8" if required).
