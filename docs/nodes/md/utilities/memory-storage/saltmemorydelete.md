# Memory Delete

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node deletes a specific key from execution-scoped memory associated with the current workflow execution. It validates the provided key, ensures an execution context is available, and then calls the underlying storage backend to remove the value. The node returns a boolean indicating whether a value was actually deleted.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/memory-storage/saltmemorydelete.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to clear or invalidate a value stored in execution-scoped memory during a workflow run. Typical use cases include: (1) removing transient flags or state after they are no longer needed, (2) resetting parts of a conversation or state machine, and (3) cleaning up sensitive data after processing. It is usually placed downstream of nodes that read or write execution memory (for example, after a memory read or write operation) as a cleanup or reset step. Common upstream nodes include those that decide which key to delete (such as conditional logic or controller nodes) and memory writer nodes that previously stored the key. Downstream nodes often check the returned boolean to branch the workflow (for example, if an item was successfully deleted versus not found). Integrate it alongside nodes like SaltMemoryWrite and SaltMemoryRead for full lifecycle management of in-execution state. For best results, keep your key naming consistent across your workflow, validate that the key is constructed identically everywhere, and ensure the node runs within a proper workflow execution context so the execution ID is available.

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
<tr><td style="word-wrap: break-word;">key</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The execution memory key to delete. Must be a valid storage key according to the "storage-key" validator (for example, no illegal characters, usually lowercase with delimiters). The key must exactly match what was used when storing the value in execution memory.</td><td style="word-wrap: break-word;">session:user_profile_stage</td></tr>
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
<tr><td style="word-wrap: break-word;">deleted</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Indicates whether a value was actually deleted for the specified key in this execution's memory. true means the key existed and was removed; false typically means no value was stored under that key for this execution.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Deleting a single key is typically a fast operation, but repeated deletes in tight loops can add overhead depending on the backing storage implementation.
- **Limitations**: The node operates only on execution-scoped memory; it cannot delete data from other scopes such as long-term or user-global storage if those are implemented separately.
- **Behavior**: If workflow_execution_id is not available in the current context, the node raises a ValueError instead of silently failing, which can halt the workflow until fixed.
- **Behavior**: Deletion is idempotent at the storage layer—calling this node multiple times with the same key in the same execution will return true only if the key existed at the time of that call, and false once it is already gone.

## Troubleshooting
- **Execution ID is not available in this context.**: This error indicates workflow_execution_id was not set (for example, running the node outside a normal workflow run). Ensure the workflow is executed through Salt's runtime and that the node is not being tested in isolation without context.
- **Key fails validation (invalid storage-key)**: If the UI or logs show validation errors for the key, adjust the key format to meet storage-key rules (for example, avoid spaces or special characters, and use a consistent delimiter such as : or _).
- **Deleted is always false even when you expect data**: Confirm that you are using the same key string used when writing the memory entry and that the write happened in the same workflow_execution_id. Also verify that upstream memory write nodes executed successfully and in the same execution branch.
- **Intermittent deletion behavior across branches**: If different branches use different key naming schemes, you may be attempting to delete a key that was never set in the current branch. Standardize key naming across the workflow and verify branching logic before the delete node.
