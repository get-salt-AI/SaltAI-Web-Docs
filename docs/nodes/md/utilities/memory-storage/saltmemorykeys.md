# Memory Keys

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node retrieves the list of stored memory keys for the current workflow execution, optionally filtering them by a key prefix. It returns the keys as a JSON-encoded string along with the total count. It can also trigger a full cleanup of all memory for the current execution after listing, making it useful for both inspection and teardown of transient state.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/memory-storage/saltmemorykeys.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to inspect or manage the in-memory key-value entries associated with the current workflow execution. Typical scenarios include debugging stored intermediate results, enumerating what has been cached in memory, or cleaning up memory at the end of a run. Place it downstream from nodes that write to memory using other Storage/Memory nodes (such as nodes that store conversation state, intermediate computation outputs, or cached API responses). Downstream, you can feed its keys output (JSON string) into parser or logic nodes that select or iterate over specific keys, or simply log it for observability. The optional prefix lets you limit results to a subset of keys (for example, only keys starting with step. or conversation.). The cleanup flag performs a full memory cleanup for the current execution after listing keys, which is especially useful at the tail end of a workflow to avoid memory buildup. Use this in conjunction with related storage nodes like SaltMemoryGet, SaltMemorySet, or other Storage category nodes to build robust stateful workflows.

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
<tr><td style="word-wrap: break-word;">prefix</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional filter to only return keys that start with this prefix. If left empty or only whitespace, all keys for the current execution are listed. Prefix matching is simple string prefix; it is not a regex or glob.</td><td style="word-wrap: break-word;">step.</td></tr>
<tr><td style="word-wrap: break-word;">cleanup</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When true, deletes ALL memory keys for this workflow execution after listing them. This is not limited to the provided prefix; it affects all keys tied to the current execution context. Use with caution, typically at the end of a workflow when no further memory reads are required.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">keys</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-encoded list of memory keys for the current execution, optionally filtered by the provided prefix. The structure is a JSON array of strings, which you can parse downstream to inspect or iterate over specific keys.</td><td style="word-wrap: break-word;">["step.input_text", "step.summary", "conversation.history_01"]</td></tr>
<tr><td style="word-wrap: break-word;">count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The total number of keys returned in the keys list. This is the length of the key array after prefix filtering (if a prefix is used). It is useful for quick checks, conditional branching, or monitoring.</td><td style="word-wrap: break-word;">3</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Listing keys is generally lightweight, but if an execution has accumulated a very large number of memory entries, the JSON payload can grow and impact downstream processing time.
- **Limitations**: The prefix filter is a simple string prefix; it cannot match substrings in the middle of keys or support wildcards or regular expressions.
- **Behavior**: When cleanup is enabled, the node deletes ALL memory keys for the current execution, not just those matching the prefix filter. After cleanup, other nodes in the same execution that try to read memory keys may find nothing.
- **Behavior**: If workflow_execution_id is not available in the execution context, the node will raise an error instead of returning an empty list; this typically indicates the node is being used outside a proper execution context or the platform failed to inject IDs.
- **Behavior**: The keys output is always a string containing JSON, not a native list type. Downstream nodes must parse it if they need structured access to individual keys.

## Troubleshooting
- **Execution ID is not available in this context.**: This error occurs when workflow_execution_id is missing. Ensure the node is used within a normal Salt workflow execution and not in an isolated test context that lacks execution metadata.
- **Got empty key list when keys are expected**: If keys is [] but you expect entries, verify that upstream storage nodes wrote data under the same workflow_execution_id and that cleanup was not enabled earlier in the workflow.
- **Unexpected data type downstream (string vs list)**: If a downstream node fails because it expects a list but receives the keys string, remember that this output is JSON-encoded. Add a JSON parsing or transformation node before consuming the keys as a list.
- **Data missing after using cleanup**: If subsequent nodes cannot find memory entries, check whether cleanup was set to true in this node or an earlier Memory Keys node. Disable cleanup during debugging or move cleanup to the very end of the workflow.
