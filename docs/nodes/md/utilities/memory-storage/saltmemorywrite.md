# Memory Write

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node stores a JSON-serializable value in Salt's execution-scoped memory under a user-defined key. It can overwrite any existing value or append new data to an existing list, automatically creating or converting to a list when needed. Optional metadata tags can be attached for later filtering, auditing, or retrieval logic.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/memory-storage/saltmemorywrite.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Memory Write node when you need to persist intermediate results, context, or state across different steps of a single workflow execution. Typical scenarios include storing model responses for later analysis, accumulating a history of messages or events, caching preprocessed data for reuse in downstream nodes, or tagging data with metadata (for example, stage, user, experiment) for inspection and monitoring.

Place this node downstream of computation or data-producing nodes (for example: language model response nodes, tool-calling nodes, data transformation nodes) to capture their outputs. Downstream, you typically pair it with a memory read node such as "SaltMemoryRead" or other storage/query nodes that consume the stored key, data, and metadata. The "mode" field controls behavior: use "set" when you want a single latest value per key, and "append" when you want to build an ordered list of values over time (such as a log of user queries or model answers).

Common patterns: (1) write each step's output with a distinct key like "step-3-summary", (2) append incremental messages to a conversation log key like "chat-history", and (3) tag stored values with JSON metadata like {"stage": "preprocessing", "dataset": "support_tickets"} for monitoring or offline analysis. Best practices: define a consistent naming scheme for keys per workflow, ensure values are strictly JSON-serializable, and provide valid JSON in metadata_tags when using metadata. Memory is scoped per workflow_execution_id, so keys do not collide across independent runs.

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
<tr><td style="word-wrap: break-word;">key</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Unique identifier for this memory entry within a single workflow execution. Must satisfy storage-key validation (non-empty and without disallowed characters). If a value already exists at this key, how it is handled depends on mode (overwrite or append).</td><td style="word-wrap: break-word;">chat-history</td></tr>
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The data to store. Must be JSON-serializable: allowed types include null, booleans, numbers, strings, lists, and dictionaries composed of JSON-serializable values. Bytes are accepted only if they contain valid JSON. Complex objects should be converted into JSON-compatible structures before use.</td><td style="word-wrap: break-word;">[{"role": "user", "content": "How can I reset my password?"}, {"role": "assistant", "content": "You can reset it from the account settings page."}]</td></tr>
<tr><td style="word-wrap: break-word;">mode</td><td>True</td><td style="word-wrap: break-word;">["set", "append"]</td><td style="word-wrap: break-word;">"set" overwrites any existing value at this key with the new value. "append" reads any existing value, converts it to a list if needed, and appends the new value to that list (or creates a new one-element list if the key is empty).</td><td style="word-wrap: break-word;">append</td></tr>
<tr><td style="word-wrap: break-word;">metadata_tags</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional JSON object encoded as a string, containing metadata tags to attach to this memory record. Keys and values should be JSON-serializable. Used for search, filtering, and analytics. If invalid JSON, parsing may fail and cause an error.</td><td style="word-wrap: break-word;">{"stage": "response_generation", "run": "42", "team": "support"}</td></tr>
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
<tr><td style="word-wrap: break-word;">key</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The key under which the value was stored. Useful for passing into downstream memory read nodes or for logging which key was written.</td><td style="word-wrap: break-word;">chat-history</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The stored value as a string. If the original value is not a plain string, it is JSON-encoded. Downstream nodes can parse this string as JSON to reconstruct the original list, dictionary, or scalar.</td><td style="word-wrap: break-word;">[{"role": "user", "content": "How can I reset my password?"}, {"role": "assistant", "content": "You can reset it from the account settings page."}]</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string representing the storage service's response for this write, including details such as execution ID, key, timestamps, and any stored metadata. Primarily used for debugging, logging, or advanced conditional flows.</td><td style="word-wrap: break-word;">{"execution_id": "exec_a1c5b6de-f3d0-4e56-8d19-9a1c4db3bd21", "key": "chat-history", "updated_at": "2026-04-20T09:30:15.123Z", "metadata": {"stage": "response_generation", "run": "42"}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: In append mode, the node performs a read-modify-write cycle, which can add latency if you append very frequently to the same key; consider batching or fewer, larger writes when throughput matters.
- **Limitations**: Values must be JSON-serializable. Custom classes, complex objects, or arbitrary binary data will fail; convert them to combinations of dictionaries, lists, strings, numbers, booleans, and null before writing.
- **Behavior**: In append mode, if the existing value at a key is not a list (for example, a single string or dictionary), it is wrapped together with the new value into a list, changing the stored type for that key.
- **Behavior**: Memory is scoped by workflow_execution_id. The same key name used in different executions refers to different records, so cross-run sharing requires explicit design.
- **Behavior**: Empty or whitespace-only metadata_tags results in no metadata attached. Invalid JSON in metadata_tags can cause parsing errors rather than being silently corrected.

## Troubleshooting
- **Error: Cannot serialize value of type 'X' to JSON**: The value contains unsupported types (for example, custom objects). Convert it into basic JSON-compatible types before passing it to this node.
- **Error: Cannot serialize bytes to JSON. Got N bytes that are not valid JSON.**: A bytes payload does not contain valid JSON text. Decode and parse upstream or change the producing node to output JSON-serializable structures instead of raw bytes.
- **Error: Execution ID is not available in this context.**: The node is running without a workflow_execution_id, which usually means it is not running inside a proper Salt workflow execution. Ensure it is invoked as part of a standard workflow run.
- **Validation error on key (storage-key)**: The key does not meet storage-key constraints (for example, empty or contains disallowed characters). Change it to a simple, safe identifier like "session-1-log" or "summary_step_3".
