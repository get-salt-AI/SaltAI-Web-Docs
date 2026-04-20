# Oracle Database Info

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node connects to an Oracle database using stored credentials and calls the `/database-info` operation of the Oracle service. It returns structured metadata about the database instance, such as name, version, edition, and platform, formatted as human-readable text plus JSON. The node is designed for quick environment inspection and monitoring, not for querying application data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/connectors/oracle/saltoracledatabaseinfo.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to confirm which Oracle instance a workflow is connected to and capture basic environment details before running queries or schema operations. Typical scenarios include validating that a pipeline is targeting the right environment (development vs staging vs production), gathering configuration details for audits or change management, and collecting context when diagnosing connection or performance issues. In a typical workflow, an upstream configuration or credentials node provides the `credentials_path`, and a global or per-node setting supplies the `timeout`. Downstream, you might send the text output to logging or notification nodes, and parse the JSON output in logic or control nodes to branch on database properties (for example, executing certain steps only when the database name matches a specific value). This node pairs well with `SaltOracleTestConnection` (to ensure the connection is valid) and with exploration nodes such as `SaltOracleListSchemas`, `SaltOracleListTables`, and `SaltOracleTableInfo` for deeper structural inspection once the instance has been identified.

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
<tr><td style="word-wrap: break-word;">credentials_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Path or identifier to the saved Oracle credentials configuration. This must match the `oracle` credential template used by the Oracle service and typically includes host, port, service or SID, username, password, and other connection options. If the file is missing, malformed, or references an unreachable server, the node will fail when attempting to load credentials or make the request.</td><td style="word-wrap: break-word;">/workspace/credentials/oracle/prod_oracle.json</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the Oracle `/database-info` request before treating it as failed. Use higher values for remote or heavily loaded databases, and lower values when you want rapid feedback on connectivity issues. A timeout that is too low can cause spurious failures, while very high values may delay error detection.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">text_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Human-readable summary of the Oracle database instance information. This is suitable for logs, dashboards, or quick inspection in the UI, and usually includes items such as the database name, version, edition, and host or service details.</td><td style="word-wrap: break-word;">Oracle Database Info: Name: ORCLPROD Version: 19.0.0.0.0 Edition: Enterprise Edition Host: prod-db01.internal Service: ORCLPROD</td></tr>
<tr><td style="word-wrap: break-word;">json_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-serialized payload with the complete database info returned by the service. Downstream nodes can parse this string to extract individual fields for logic, reporting, or conditional execution. The structure depends on the underlying Oracle service, but commonly includes database_name, version, edition, host, service_name, platform, and status.</td><td style="word-wrap: break-word;">{"database_name": "ORCLPROD", "version": "19.0.0.0.0", "edition": "Enterprise Edition", "host": "prod-db01.internal", "service_name": "ORCLPROD", "platform": "Linux x86_64", "status": "OPEN"}</td></tr>
<tr><td style="word-wrap: break-word;">html_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML-formatted representation of the database info, when provided by the shared database base node formatter. This is useful for embedding a nicely formatted summary in web-based dashboards or documentation views. If the formatter does not generate HTML for this operation, this field may be an empty string.</td><td style="word-wrap: break-word;"><h2>Oracle Database Info</h2><table><tr><th>Name</th><td>ORCLPROD</td></tr><tr><th>Version</th><td>19.0.0.0.0</td></tr><tr><th>Edition</th><td>Enterprise Edition</td></tr></table></td></tr>
<tr><td style="word-wrap: break-word;">file_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Reference to any file export produced by the common formatting logic (often empty for simple info calls). When present, it may point to a JSON or HTML file containing the same database info for archival, download, or external reporting.</td><td style="word-wrap: break-word;">/workspace/outputs/oracle/database_info_ORCLPROD_2024-03-01.json</td></tr>
<tr><td style="word-wrap: break-word;">extra_output</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Auxiliary or diagnostic data emitted by the shared base node, such as request identifiers or execution metadata. Many workflows ignore this output, but it can be helpful for debugging or tracing executions.</td><td style="word-wrap: break-word;">{"request_id": "a3f1bc27-9fa4-4f1a-8d3e-21a5b2c4f0de", "execution_time_ms": 142}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs a single metadata request and is generally fast, but overall latency depends on network distance and Oracle server load; increase the `timeout` for high-latency or busy environments.
- **Limitations**: It exposes instance-level information only and does not list schemas, tables, or columns; use nodes like `SaltOracleListSchemas`, `SaltOracleListTables`, or `SaltOracleTableInfo` for structural exploration.
- **Behavior**: Connection handling, API invocation, and result formatting are delegated to a shared database base node and the Oracle backend service, so the exact JSON field names and structure may vary slightly between deployments.
- **Behavior**: On connection or permission failures, the outputs may contain an error object instead of normal metadata; always check for error fields in the JSON string if you are using it programmatically.
- **Security**: The node uses credentials referenced by `credentials_path` and does not emit raw passwords, but the outputs can still contain sensitive environment details such as internal hostnames or service names; avoid exposing them in publicly visible logs.

## Troubleshooting
- **Common Error 1**: Connection-related errors such as "Failed to connect" or similar messages – confirm that `credentials_path` points to a valid Oracle credential file, verify host/port/service values, and ensure the Oracle server is reachable from the execution environment.
- **Common Error 2**: Timeouts when requesting database info – increase the `timeout` value, check for network latency or firewall issues, and verify that the Oracle server is not overloaded or paused for maintenance.
- **Common Error 3**: Empty or minimal info in `json_output` – this usually indicates insufficient privileges for the Oracle user to read instance metadata or a backend error; work with your DBA to grant appropriate permissions or inspect backend logs.
- **Common Error 4**: Downstream parsing failures – remember that `json_output` is a JSON string, not a native object; parse it before accessing fields, and implement error handling in case the payload represents an error instead of normal database info.
