# Memory Read

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

The Memory Read node retrieves data or metadata from Salt's execution-scoped memory by a specified key. It can either return the stored value plus metadata, or perform a metadata-only lookup to check existence without loading the value. The node offers flexible handling for missing keys, allowing workflows to fail fast, return empty, or fall back to a user-defined default JSON value.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/memory-storage/saltmemoryread.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to fetch previously stored data associated with the current workflow execution, such as intermediate results, cached responses, or coordination flags between branches. It typically appears downstream of nodes that write to memory (for example, "SaltMemoryWrite" or other Storage nodes) and upstream of processing nodes that consume the retrieved value (e.g., text processing, branching/condition, or API call nodes). A common pattern is: compute a result, store it with "SaltMemoryWrite" under a stable key, then later in the workflow or in a different branch, use "Memory Read" with the same key to reuse that result without recomputation. Choose read_mode = "Value" when you need the actual stored data, or read_mode = "Metadata Only" when you only need to know if a key exists, its size, timestamps, or other metadata to drive conditional logic. For missing keys, you can configure missing_key_behavior to match your use case: "Fail" to enforce required data, "Return empty" when an absent value is acceptable but should result in an empty payload, or "Return default" to supply a custom JSON fallback (for example, an empty configuration object or placeholder content). This node works best in workflows that coordinate multiple runs of the same pipeline or share state between parallel branches using the memory storage layer.

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
<tr><td style="word-wrap: break-word;">key</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The memory key to read from execution-scoped storage. Must be a valid storage key (validated as a "storage-key"), typically lowercase with safe characters and no surrounding whitespace. The key must match exactly what was used when writing the value.</td><td style="word-wrap: break-word;">session:user-48293:profile</td></tr>
<tr><td style="word-wrap: break-word;">read_mode</td><td>True</td><td style="word-wrap: break-word;">ENUM (READ_MODES)</td><td style="word-wrap: break-word;">Determines what is retrieved for the given key. In "Value" mode, the node loads the stored data and its metadata. In "Metadata Only" mode, it performs a lightweight check that returns only metadata and existence information without loading the full value, which is useful for conditional logic or performance-sensitive checks.</td><td style="word-wrap: break-word;">Value</td></tr>
<tr><td style="word-wrap: break-word;">missing_key_behavior</td><td>False</td><td style="word-wrap: break-word;">ENUM (MISSING_KEY_BEHAVIORS)</td><td style="word-wrap: break-word;">Specifies what to do if the key does not exist in memory. Options include: "Fail" (raise an error and stop the workflow), "Return empty" (return an empty result while marking exists as false), and "Return default" (return the JSON provided in default_value as the data). If not set, it defaults to failing when the key is missing.</td><td style="word-wrap: break-word;">Return default</td></tr>
<tr><td style="word-wrap: break-word;">default_value</td><td>False</td><td style="word-wrap: break-word;">STRING (JSON)</td><td style="word-wrap: break-word;">JSON-formatted fallback value that is used only when missing_key_behavior is set to "Return default" and the key is missing. Must be valid JSON and will be returned as the data output. It is displayed in JSON mode and supports multiline content.</td><td style="word-wrap: break-word;">{ "status": "unknown", "attempts": 0 }</td></tr>
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
<tr><td style="word-wrap: break-word;">key</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The key that was requested, returned unchanged. This is useful for passing the key forward along with the data, logging, or joining with other branches that rely on the same key name.</td><td style="word-wrap: break-word;">session:user-48293:profile</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">STRING (JSON)</td><td style="word-wrap: break-word;">The stored value serialized as a JSON string when read_mode is "Value" and the key exists. If the key is missing and missing_key_behavior is "Return default", this contains the default JSON value. In metadata-only mode, this will be an empty string since no value is loaded.</td><td style="word-wrap: break-word;">{ "name": "Alex Rivera", "plan": "pro", "onboardingComplete": true }</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING (JSON)</td><td style="word-wrap: break-word;">Metadata about the memory entry serialized as JSON. Contents depend on the storage implementation but typically include fields such as timestamps, size, content type, or custom tags. In both value and metadata-only modes, this is the primary place to inspect existence and characteristics beyond the raw value.</td><td style="word-wrap: break-word;">{ "created_at": "2026-04-20T11:15:03Z", "updated_at": "2026-04-20T11:16:10Z", "bytes": 1248 }</td></tr>
<tr><td style="word-wrap: break-word;">exists</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Indicates whether the key exists in memory. It is true when the key was found and read successfully, and false when the key is missing and the chosen missing_key_behavior allowed the workflow to continue. Use this to drive conditional branching or fallback logic.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Using "Metadata Only" read mode avoids loading the full value and is recommended when you only need to check if a key exists or to inspect basic metadata before deciding whether to read the full data.
- **Limitations**: The node operates on execution-scoped memory; data written in one workflow execution is not visible to another unless explicitly designed in the storage layer, so you cannot use it as a cross-execution global cache by default.
- **Behavior**: If workflow_execution_id is missing (such as running outside a normal workflow context), the node raises an error and does not attempt any read, ensuring you do not accidentally access unscoped storage.
- **Behavior**: When a key is missing and missing_key_behavior is set to "Fail", the node raises an error instead of returning exists = false, so downstream nodes should expect that the workflow stops rather than receiving an empty result.
- **Behavior**: Both data and metadata are returned as JSON strings; downstream nodes often need to parse these as JSON if they require structured access to fields.

## Troubleshooting
- **Missing execution context**: Error message like "Execution ID is not available in this context." indicates the node is being run without a workflow_execution_id. Ensure the node is used within a normal Salt workflow execution and not in an unsupported testing or ad-hoc context.
- **Key not found with Fail behavior**: If the workflow stops with an error related to a missing memory key, check that missing_key_behavior is not set to "Fail" unless the key is guaranteed to exist. For optional data, choose "Return empty" or "Return default" instead.
- **Unexpected empty data**: If data is an empty string, verify that read_mode is set to "Value". In "Metadata Only" mode, the node intentionally does not return the stored value and leaves data empty.
- **Invalid default JSON**: When using "Return default", if downstream nodes fail while parsing the data output, the cause is often malformed JSON in default_value. Validate that default_value is syntactically correct JSON and matches the structure expected by consumers.
