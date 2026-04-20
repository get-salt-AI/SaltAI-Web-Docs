# Memory Delete

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node removes a single key from execution-scoped memory tied to a specific workflow execution. It validates the provided key, ensures it is used within an active execution context, and then calls the storage backend to delete the entry. The node returns a boolean indicating whether the key was successfully deleted.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/memory-storage/saltmemorydelete.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to clear or revoke previously stored execution-specific state, such as temporary flags, intermediate results, or cached responses. It typically appears after one or more storage or processing nodes that have written to execution memory (for example, nodes that save per-run configuration, user decisions, or partial outputs) and before downstream logic that must not see outdated data. Common upstream nodes include memory write or update nodes (for example, SaltMemorySet, SaltMemoryUpdate) and decision logic that determines which keys to clean up. Downstream, it often feeds into conditional or control-flow nodes that branch based on the deleted flag to confirm cleanup or trigger fallback behavior. Use it to implement idempotent workflows (clearing keys at the end of a run), to reset parts of conversation or session state mid-run, or to remove sensitive values no longer needed. Best practice is to use clearly namespaced keys (such as user/profile-cache or session/step-3) and to pair each write with an appropriate cleanup step when data should not persist for the entire execution.

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
<tr><td style="word-wrap: break-word;">key</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The execution memory key to delete. This must be a valid storage key according to the platform's storage-key validation rules, which typically enforce constraints like non-empty strings, disallowing unsafe characters, and encouraging simple, path-like names. The key is resolved within the current workflow execution's memory namespace and cannot delete data from other executions or global scopes.</td><td style="word-wrap: break-word;">chat/session_state</td></tr>
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
<tr><td style="word-wrap: break-word;">deleted</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Indicates whether the given key existed and was successfully deleted from execution memory. True means the storage client acknowledged the delete, while false usually means the key was not present. Downstream nodes can branch on this to handle both the case where data was actually cleaned up and where there was nothing to remove.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Deletion is a single key operation against the storage backend and is generally fast, but frequent deletes in tight loops may still incur overhead depending on the storage implementation.
- **Limitations**: The node only operates on execution-scoped memory; it cannot delete user-scoped, global, or external storage keys that are not bound to the current workflow execution.
- **Behavior**: If workflow_execution_id is not available, the node will raise a ValueError stating that the execution ID is not available, and no delete will be attempted.
- **Behavior**: The deleted output reflects the storage client's memory_delete result; if the key did not exist, the node may return false without error, which is normal and should be handled explicitly in your workflow logic.
- **Behavior**: Key validation happens before deletion; invalid keys such as empty strings or disallowed formats will cause an immediate validation error rather than calling the storage backend.

## Troubleshooting
- **Execution ID is not available in this context.**: This error occurs when workflow_execution_id is missing, often because the node is being executed outside a normal workflow run or the execution context is misconfigured. Ensure the node is used inside a standard Salt workflow execution and that the engine is correctly propagating execution IDs.
- **Key validation failed or invalid storage key.**: If the provided key does not pass storage-key validation, the node will raise an error before attempting deletion. Adjust the key to follow allowed formats, such as simple names or path-like keys without illegal characters.
- **Deleted is always false.**: If you consistently get deleted = false, it usually means the key does not exist in execution memory at the time of deletion or is being written under a different key name. Verify that upstream nodes are writing under the exact same key string.
- **Storage client errors.**: If underlying storage is unavailable or misconfigured, you may see exceptions from the storage client when calling memory_delete. Check platform logs for storage connectivity issues and confirm that the storage service and credentials are correctly configured.
