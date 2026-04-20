# Storage List & URL

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node lets you inspect Salt storage and generate time-limited download links. Depending on the selected operation, it can list keys within a namespace, list all available namespaces for the current user, or create a signed URL to download a specific stored key. Results are returned as JSON strings for listings or a direct URL string for downloads, plus a count of returned items.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/memory-storage/saltstoragelisturl.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to discover what is stored in Salt storage or share or download a stored artifact via a secure URL. Common scenarios include listing saved outputs from previous workflow runs filtered by prefix or tags, enumerating all namespaces assigned to a user to understand storage organization, and generating a signed download URL to provide a user or external system with access to a specific result file for a limited time. Place this node downstream of any workflow that writes to storage (for example a storage save node or other Salt storage writer nodes) to inspect or retrieve what was saved. Its outputs are often consumed by nodes that parse JSON (to work with key lists) or by notification or integration nodes that can deliver the signed URL. Choose operation = "List Keys" when you know the namespace and want to filter results by key prefix or tags, operation = "List Namespaces" to discover where data is stored, and operation = "Get Signed URL" when you know the exact key path and need a direct download URL that expires after a configurable number of hours.

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
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">CHOICE[List Keys\|List Namespaces\|Get Signed URL]</td><td style="word-wrap: break-word;">Determines the action performed. "List Keys" returns a JSON list of keys in a namespace, optionally filtered by prefix and tags. "List Namespaces" returns a JSON list of all namespaces for the current user and ignores namespace, prefix, and tags inputs. "Get Signed URL" generates a temporary download link for a single key using namespace, key_path, and expiration_hours.</td><td style="word-wrap: break-word;">List Keys</td></tr>
<tr><td style="word-wrap: break-word;">namespace</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Logical container in storage to scope listings or signed URLs. Used by "List Keys" and "Get Signed URL" and ignored for "List Namespaces". If left blank or whitespace, it defaults to "default". Must match the namespace under which your keys were saved.</td><td style="word-wrap: break-word;">experiment-2025-01</td></tr>
<tr><td style="word-wrap: break-word;">prefix</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Filters listed keys to those starting with this prefix. Only used when operation is "List Keys". Useful to group related outputs under a run or folder. Empty string means no prefix filter.</td><td style="word-wrap: break-word;">runs/42/results.</td></tr>
<tr><td style="word-wrap: break-word;">tags</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of key=value filters applied when listing keys. Only used when operation is "List Keys". Tags must match how they were stored, such as model and stage. Empty string means no tag filter.</td><td style="word-wrap: break-word;">model=gpt-4.1,stage=final,customer=acme</td></tr>
<tr><td style="word-wrap: break-word;">key_path</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Full storage key path for which to generate a signed URL. Only used when operation is "Get Signed URL". Must be a valid storage key and must exist in the specified namespace, otherwise an error is raised.</td><td style="word-wrap: break-word;">runs/42/results.summary.json</td></tr>
<tr><td style="word-wrap: break-word;">expiration_hours</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Duration, in hours, for which the signed URL remains valid. Only used for "Get Signed URL". Must be between 1 and 168 inclusive (up to 7 days). Larger values are not allowed.</td><td style="word-wrap: break-word;">24</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Primary result of the selected operation. For "List Keys" and "List Namespaces", this is a JSON-encoded array of items returned by the storage client (for example a list of key descriptors or namespaces). For "Get Signed URL", this is the signed download URL string itself.</td><td style="word-wrap: break-word;">[{"key": "runs/42/results.summary.json", "namespace": "experiment-2025-01"}, {"key": "runs/43/results.summary.json", "namespace": "experiment-2025-01"}]</td></tr>
<tr><td style="word-wrap: break-word;">count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of items represented by the result. For "List Keys" and "List Namespaces", this is the length of the returned list. For "Get Signed URL", the value is always 1 because it returns a single URL.</td><td style="word-wrap: break-word;">2</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Use the prefix and tags filters when listing keys to reduce response size and latency, especially in namespaces with many stored objects.
- **Limitations**: The expiration_hours value for signed URLs is capped at 168 hours (7 days); higher values are rejected by validation.
- **Behavior**: For listing operations, the result output is always a JSON string, not a parsed structure; downstream nodes must parse it to work with individual items.
- **Behavior**: If the specified key does not exist for the "Get Signed URL" operation, the node maps the underlying 404 to a clear runtime error message indicating that the key was not found in the given namespace.
- **Security**: Signed URLs grant direct read access to the underlying object until they expire; share them only with trusted parties and keep expiration_hours as low as practical.

## Troubleshooting
- **Key not found in namespace**: When using "Get Signed URL", this means key_path does not exist in the specified namespace. Verify the namespace, trim whitespace, and first use "List Keys" to confirm the exact key path.
- **User ID is not available in this context.**: The runtime did not provide salt_user_id. Ensure the workflow is running under an authenticated Salt user and that hidden inputs have not been altered or removed.
- **Unknown operation error**: If you encounter an error mentioning an unknown operation, the operation input is set to an unsupported value. Reconfigure the node and choose one of: "List Keys", "List Namespaces", or "Get Signed URL".
- **Invalid key path**: If key_path fails validation for "Get Signed URL", the node will raise a validation error. Ensure the key is non-empty, uses a supported format, and matches an existing key, for example by copying it from a previous "List Keys" output.
