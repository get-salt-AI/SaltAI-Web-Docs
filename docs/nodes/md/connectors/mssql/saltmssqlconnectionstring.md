# SQL Server Connection String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node constructs a Microsoft SQL Server (MSSQL) connection string from discrete parameters including host, port, database name, credentials, and integrated security. It centralizes and standardizes database connection configuration so other nodes can reuse a consistent URI. The node is tailored to MSSQL, with support for optional instance names and Windows integrated security.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/mssql/saltmssqlconnectionstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever downstream nodes require a MSSQL connection string or database URI rather than individual connection fields. Place it near the start of your workflow, before any node that executes SQL queries, imports data from MSSQL, or performs schema operations (for example, generic database query or CData MSSQL operation nodes that accept a DATABASE_URI or connection_string input). Upstream, configure environment-specific details such as host, port, database, and credentials, potentially sourced from Salt’s credential management using the mssql template. Downstream, feed the generated connection string into nodes like MSSQL query executors, data extraction nodes, or orchestration steps that need a ready-to-use MSSQL URI. For best practice, define one connection node per environment (dev, test, prod) and reuse it across multiple pipelines so that connection details are maintained in a single place.

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
<tr><td style="word-wrap: break-word;">host</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Hostname or IP address of the MSSQL server. This must be reachable from the Salt runtime environment. Do not include protocol prefixes; supply only the DNS name or IP address.</td><td style="word-wrap: break-word;">sql-prod.internal.company.local</td></tr>
<tr><td style="word-wrap: break-word;">port</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">TCP port on which the MSSQL server listens. Must be between 1 and 65535. Defaults to 1433, the standard MSSQL port. Change this if your server uses a non-default port.</td><td style="word-wrap: break-word;">1433</td></tr>
<tr><td style="word-wrap: break-word;">database</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target database name on the MSSQL server. The connecting user or identity must have appropriate permissions on this database. Defaults to master, but typically you will specify a specific application or analytics database.</td><td style="word-wrap: break-word;">sales_analytics</td></tr>
<tr><td style="word-wrap: break-word;">username</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">MSSQL login username for SQL authentication. Required when integrated_security is false. In integrated security scenarios, the driver may ignore this value depending on configuration.</td><td style="word-wrap: break-word;">reporting_user</td></tr>
<tr><td style="word-wrap: break-word;">password</td><td>True</td><td style="word-wrap: break-word;">PASSWORD</td><td style="word-wrap: break-word;">Password for the MSSQL login when using SQL authentication. Treated as a sensitive field. You may leave this empty in the graph and instead rely on Salt’s secure credential storage or templates to inject the actual secret at runtime.</td><td style="word-wrap: break-word;"><mssql-strong-password></td></tr>
<tr><td style="word-wrap: break-word;">instance</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional SQL Server instance name. Use this when the server hosts named instances (for example, MSSQLSERVER or DEV01). Leave empty for the default instance. If provided, it is incorporated into the constructed connection string according to MSSQL conventions.</td><td style="word-wrap: break-word;">MSSQLSERVER</td></tr>
<tr><td style="word-wrap: break-word;">integrated_security</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to use Windows integrated security (SSPI) instead of SQL authentication. When true, the connection uses the identity of the Salt runtime environment, and username/password may be ignored by the driver. Ensure the runtime identity has the necessary database permissions.</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">connection_string</td><td style="word-wrap: break-word;">DATABASE_URI</td><td style="word-wrap: break-word;">A ready-to-use MSSQL connection string encoding host, port, database, credentials, optional instance, and integrated security options. Pass this directly into downstream database or CData nodes that expect a database URI or connection string for MSSQL.</td><td style="word-wrap: break-word;">mssql://reporting_user:<mssql-strong-password>@sql-prod.internal.company.local:1433/sales_analytics?instance=MSSQLSERVER&integratedSecurity=false</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node’s own processing is negligible; performance impact stems from how often downstream components open new connections using the generated URI. Reuse a single connection string node across multiple queries or operations when possible.
- **Limitations**: This node is specific to MSSQL and does not expose every possible driver- or provider-specific option (such as fine-grained encryption flags, connection timeouts, or pooling parameters). For advanced scenarios you may need custom configuration or nodes.
- **Behavior**: When integrated_security is enabled, the underlying driver may ignore username and password. Make sure the OS or service account under which Salt runs has appropriate permissions on the target MSSQL database.
- **Behavior**: The node is configured with credential_template and service_name set to mssql, integrating it with Salt’s MSSQL credential management. Prefer managing passwords and secrets centrally there rather than embedding them directly in node parameters.

## Troubleshooting
- **Authentication failed for user**: If you see messages like Login failed for user 'reporting_user', check that integrated_security is false for SQL authentication, confirm the username and password, and verify the login is enabled and not locked.
- **Connection timeout or cannot connect to server**: Timeouts or unreachable host errors usually indicate network or configuration issues. Confirm host resolves from the runtime, port matches the SQL Server configuration, and that firewalls or security rules allow access on that port.
- **Database does not exist or access denied**: Errors indicating the database cannot be opened or is unknown typically mean the database value is incorrect or your user/identity lacks permissions. Verify the database name and user grants on that database.
- **Instance-related connection failures**: If connections fail only when instance is set, verify the instance name is correct, the SQL Server Browser service (if required) is running, and that the instance is accessible on the specified port. Test connectivity from a SQL client using the same host, instance, and port values.
