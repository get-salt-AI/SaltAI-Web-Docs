# Storage Delete

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node removes a single key from Salt's cross-run persistent storage for a specified user and namespace. It validates the provided key path, normalizes the namespace, and uses the underlying storage client to perform the deletion. The node returns a boolean indicating whether a key was actually deleted, enabling conditional branching based on cleanup success.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/memory-storage/saltstoragedelete.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to clear or reset persistent values that are no longer needed across workflow runs, such as cached outputs, per-user flags, or workflow progress markers. It typically appears alongside storage read/write nodes (like `SaltStorageGet` and `SaltStorageSet`) as part of a state management strategy or cleanup phase. A common pattern is: read a stored value, act on it, then delete it to prevent reprocessing in subsequent runs. Upstream, nodes compute or select the `key_path` (for example, based on the current user or task); downstream, you can branch based on the `deleted` output—for example, log that cleanup occurred or handle the case when the key did not exist.

In more complex workflows, you can use this node at the end of long-running processes to remove temporary coordination keys, or during user account resets to selectively clear specific namespaces (e.g., `user_state`, `onboarding_state`) while leaving others intact. Ensure your workflow is executed in a context with a valid `salt_user_id`, because deletion is strictly user-scoped and the node will fail if no user context is available.

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
<tr><td style="word-wrap: break-word;">key_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The hierarchical key path to delete in persistent storage. Must be a valid storage key according to the platform's `storage-key` validation (non-empty, valid characters, typically dot-separated like `results.output` or `session.flags.completed`). If no key exists at this path, the node completes successfully and returns `False`.</td><td style="word-wrap: break-word;">session.flags.completed</td></tr>
<tr><td style="word-wrap: break-word;">namespace</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Namespace from which the key should be deleted. Namespaces logically group stored keys (for example, `default`, `user_state`, or `experiments`). Leading and trailing whitespace is stripped; if the resulting string is empty, it falls back to `default`.</td><td style="word-wrap: break-word;">default</td></tr>
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
<tr><td style="word-wrap: break-word;">deleted</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True if a key at the given `key_path` in the specified namespace for the current user was found and successfully deleted; False if no key was present or the storage client reported a no-op. This output is intended for branching logic or reporting in downstream nodes.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: This node deletes a single key at a time. For workflows that need to clean up many keys, consider designing key schemas and retention strategies that minimize the need for many individual deletions.
- **Limitations**: The node operates only on the exact `key_path` specified; it does not support wildcards or automatic recursive deletion of related keys. If you represent hierarchical structures via dot-separated keys, deleting a parent-like key will not automatically remove similarly prefixed keys unless your storage implementation is designed that way.
- **Behavior**: If `salt_user_id` is not available in the execution context, the node raises a `ValueError` and does not attempt any storage access. This typically signals that the workflow is being executed outside of a user-scoped context or that context propagation is misconfigured.
- **Behavior**: Whitespace around the `namespace` input is trimmed; if the trimmed value is empty, the node silently substitutes `default`. This prevents accidental writes or deletions into an unnamed namespace.
- **Limitations**: The key path is validated with a `storage-key` validator before the operation. Invalid or malformed keys cause the node to fail fast rather than perform a potentially unsafe or ambiguous deletion.

## Troubleshooting
- **Error: 'User ID is not available in this context.'**: The execution context does not provide `salt_user_id`. Ensure the workflow is triggered via a user-aware entry point (such as a logged-in user action or a user-bound API call) and that no intermediate nodes clear or override user context.
- **Validation errors on `key_path`**: Messages indicating an invalid `Key Path` or `storage-key` usually mean the key contains disallowed characters or is empty. Update upstream logic to construct valid, predictable key paths and avoid directly using raw user input as part of the key.
- **`deleted` is False when deletion was expected**: Confirm that you are using the correct combination of `namespace`, `key_path`, and user context. Check that the value was previously written under the same user and namespace and that there are no typos or unexpected trimming in the inputs.
- **Intermittent backend/storage failures**: If you see storage-related exceptions (timeouts, connection errors), the underlying storage service may be unhealthy or unreachable. Verify storage configuration, credentials, and network access, and consider adding error handling or retries in surrounding workflow logic.
