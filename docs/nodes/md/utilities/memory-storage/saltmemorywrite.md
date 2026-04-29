# Memory Write

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Memory Write saves a JSON-serializable value into Salt execution-scoped memory under a user-defined key. It can either overwrite any existing value at that key or append the new value to a list stored there, performing a read-modify-write when appending. The node also supports attaching custom metadata tags to the stored entry for later filtering, debugging, or analytics.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/memory-storage/saltmemorywrite.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use Memory Write whenever you need to persist intermediate data during a single workflow execution so that later nodes can retrieve it by key. Typical use cases include storing model responses (for example, a running conversation summary), intermediate feature representations, or accumulating logs and events across steps. Place this node downstream of data-producing nodes (such as LLM response generators, API callers, or data transformers) and upstream of memory consumer nodes, such as SaltMemoryRead or custom logic that looks up values by key.

Configure the `key` as a descriptive, stable identifier that is unique within each workflow execution (for example, "conversation.summary.v1" or "pipeline.logs.steps"). Choose `mode = "set"` for one-off values that should always reflect the latest state (like the current summary), and `mode = "append"` to build up an ordered list over multiple writes (like a chat transcript or a list of processed items). Feed any JSON-serializable `value` coming from upstream nodes. Use `metadata_tags` to encode contextual information (such as stage, environment, or run number) as a JSON object string, which can be helpful when inspecting or aggregating stored memory entries outside the workflow. This node pairs naturally with storage-related nodes like SaltMemoryRead for retrieval, and with logging/reporting nodes that consume its outputs for monitoring or debugging.

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
<tr><td style="word-wrap: break-word;">key</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier for this memory entry, scoped to a single workflow execution. Must pass storage-key validation and should be unique within an execution to avoid unwanted overwrites. Reader nodes will use this same key to look up the stored value.</td><td style="word-wrap: break-word;">conversation.summary.v1</td></tr>
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The data to store in memory. Must be JSON-serializable: allowed types include null, booleans, numbers, strings, lists, and dictionaries (possibly nested). If bytes are provided, they must contain valid JSON text. Other complex objects must be convertible to JSON, otherwise a serialization error is raised.</td><td style="word-wrap: break-word;">{"user_id": "user-123", "turn_index": 5, "summary": "User asked for a project plan and prefers weekly milestones.", "timestamp": "2026-04-27T10:37:12Z"}</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">["set", "append"]</td><td style="word-wrap: break-word;">Write strategy for this key. "set" overwrites any existing value with the new one. "append" performs a read-modify-write: if no value exists, it creates a single-element list; if a list exists, it appends the new value; if a non-list exists, it converts it into a list containing the previous and new values.</td><td style="word-wrap: break-word;">append</td></tr>
<tr><td style="word-wrap: break-word;">metadata_tags</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional metadata attached to the memory entry, encoded as a JSON object string mapping string keys to string values. Used for organization, filtering, or analytics. Must be valid JSON when provided; use an empty string to omit metadata.</td><td style="word-wrap: break-word;">{"stage": "postprocessing", "run": "42", "source": "chat-flow"}</td></tr>
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
<tr><td style="word-wrap: break-word;">key</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The key under which the value was stored. Useful for wiring into downstream nodes that log, inspect, or dynamically choose which key to read from.</td><td style="word-wrap: break-word;">conversation.summary.v1</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The stored value as a string. If the original value was not a plain string, it is returned as JSON text. Downstream nodes can parse this string to recover the structured data.</td><td style="word-wrap: break-word;">{"user_id": "user-123", "turn_index": 5, "summary": "User asked for a project plan and prefers weekly milestones.", "timestamp": "2026-04-27T10:37:12Z"}</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string representing the storage service’s response to the write operation, typically including the key, execution ID, metadata, and possibly a version or status. Useful for debugging, auditing, or flows that branch based on write results.</td><td style="word-wrap: break-word;">{"key": "conversation.summary.v1", "execution_id": "exec-20260427-10h37m-8c96", "metadata": {"stage": "postprocessing", "run": "42"}, "version": 3}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each call performs a storage write; in "append" mode the node may also perform a prior read, so frequent small appends can be more expensive than batching data and writing less often.
- **Limitations**: Only JSON-serializable values are supported. Arbitrary objects, non-JSON binary data, or extremely large and deeply nested structures may fail to serialize or cause slow writes.
- **Behavior**: In "append" mode, if the existing value at a key is not a list, it is transparently converted into a list containing the previous and new values. Downstream consumers should be prepared to handle lists when using append.
- **Behavior**: Memory is scoped by workflow execution ID. The same key used in different executions refers to separate entries and does not provide global or cross-run persistence.
- **Limitations**: If no workflow_execution_id is available (for example, outside a normal workflow run), the node raises an error and does not persist any data.

## Troubleshooting
- **Error: Cannot serialize value of type 'X' to JSON.** The value contains a non-JSON-serializable object (for example, a custom class or unsupported type). Convert it upstream into dictionaries, lists, or primitives before sending it to this node.
- **Error: Cannot serialize bytes to JSON. Got N bytes that are not valid JSON.** The input is raw bytes that do not contain valid JSON text. Decode and parse them into a structured object or convert them into a JSON-compatible string before writing.
- **Error: Execution ID is not available in this context.** The node is running without a workflow_execution_id, typically because it is not executed within a standard workflow run. Ensure the node is part of a properly started workflow execution managed by Salt.
- **Key validation failures.** If the UI or validation rejects your key, it likely contains invalid characters or format. Use a simple identifier with letters, numbers, underscores, or dots (for example, "session.summary_1") and avoid spaces and special symbols.
