# Storage Read

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

The Storage Read node fetches data previously saved in Salt's persistent storage, returning either the full value or only its metadata. It supports namespaced keys, configurable behavior when keys are missing, and decodes stored bytes into UTF-8 strings, with special handling for JSON-serialized content. This enables robust cross-run state sharing, caching, and reuse of results across workflows and executions.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/memory-storage/saltstorageread.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use Storage Read when you need to load values or metadata that were saved earlier in Salt storage, typically by a complementary storage write node. Common scenarios include reusing prior model outputs, loading cached results for expensive computations, resuming workflows from stored checkpoints, or checking that a file or record exists without downloading it. Upstream, you usually provide a literal key_path or build it dynamically using template or string-processing nodes; you can also set a namespace to isolate different projects or environments. Downstream, connect the data output to JSON parsing nodes, prompt builders, configuration loaders, or any node that consumes text or JSON, and use the exists flag and metadata to branch logic (for example, skip processing if data is missing). Pair this node with storage write nodes in the Storage category to implement patterns like "compute → write once → read in many later runs" and use missing_key_behavior plus default_value to make your pipelines resilient to missing data in staging or production.

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
<tr><td style="word-wrap: break-word;">key_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The logical key of the object to read from storage. Must be a valid storage key (non-empty, conforms to the platform's storage-key validation). This should match the key used when the object was originally written. Keys are resolved within the selected namespace.</td><td style="word-wrap: break-word;">reports/2024-03-15/summary.json</td></tr>
<tr><td style="word-wrap: break-word;">read_mode</td><td>True</td><td style="word-wrap: break-word;">READ_MODES</td><td style="word-wrap: break-word;">Controls what is fetched. "Value" downloads the stored contents and returns them along with metadata. "Metadata Only" checks for existence and returns only metadata without downloading the object body, which is useful for fast existence or size checks.</td><td style="word-wrap: break-word;">Value</td></tr>
<tr><td style="word-wrap: break-word;">namespace</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Logical namespace that scopes storage keys, helping to avoid collisions between different workflows or environments. If blank or whitespace, it defaults to "default". The same key_path in two different namespaces refers to different stored objects.</td><td style="word-wrap: break-word;">marketing-campaign-q2</td></tr>
<tr><td style="word-wrap: break-word;">missing_key_behavior</td><td>False</td><td style="word-wrap: break-word;">MISSING_KEY_BEHAVIORS</td><td style="word-wrap: break-word;">Defines how to handle cases where the key does not exist. Typical options include: "Fail" (raise an error and stop the workflow), "Return empty" (return empty outputs and mark exists as false), and "Return default" (use the default_value instead of failing). Choose an option based on how tolerant your workflow should be to absent data.</td><td style="word-wrap: break-word;">Return default</td></tr>
<tr><td style="word-wrap: break-word;">default_value</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Fallback value returned when the key is missing and missing_key_behavior is set to "Return default". This should be a JSON-formatted string that matches the structure downstream nodes expect (for example, a default configuration or an empty object/array). It is edited as JSON and can span multiple lines.</td><td style="word-wrap: break-word;">{"status":"missing","items":[]}</td></tr>
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
<tr><td style="word-wrap: break-word;">key_path</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The validated key path that was used for the read operation. Useful for downstream logging, templating, or conditional routing without needing to recompute or re-enter the key.</td><td style="word-wrap: break-word;">reports/2024-03-15/summary.json</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The stored value decoded as a UTF-8 string when read_mode is "Value". It is empty when read_mode is "Metadata Only" or when a missing-key behavior returns an empty/default result without real stored content. If the object's serialization_format is "json", this will be JSON text that downstream nodes can parse.</td><td style="word-wrap: break-word;">{"total_visits":18423,"conversion_rate":0.047,"top_channel":"email"}</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON string representing metadata for the stored object, such as size, content type, serialization_format, and timestamps. This is populated both in Value and Metadata Only modes so you can inspect properties without always loading the full object.</td><td style="word-wrap: break-word;">{"size":5123,"content_type":"application/json","serialization_format":"json","created_at":"2024-03-15T10:00:00Z"}</td></tr>
<tr><td style="word-wrap: break-word;">exists</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Indicates whether the key exists in storage and was successfully resolved, subject to the chosen missing_key_behavior. True means the object was found; false typically means it was missing but handled via a non-failing behavior such as Return empty or Return default.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Choosing "Metadata Only" avoids downloading the object body and is recommended for existence checks or when you only need size or timestamps, especially for large stored files.
- **Performance**: Thoughtful use of namespaces (for example, per-project or per-environment) does not change request speed but prevents accidental key reuse, reducing confusion and debugging time in complex systems.
- **Limitations**: The node decodes all values as UTF-8. Non-text or non-UTF-8 binary content will be decoded with replacement characters, so this node is not appropriate when workflows must preserve raw binary bytes exactly.
- **Behavior**: If salt_user_id is not available in the execution context, the node raises an error and the workflow fails. This typically indicates that the node is being run outside a fully configured Salt environment or without a bound user context.
- **Behavior**: When no serialization_format is present in metadata, the node treats the content as "raw" but still performs UTF-8 decoding with error replacement, which may slightly alter arbitrary binary contents.
- **Behavior**: The combination of missing_key_behavior and default_value directly influences the data and exists outputs; design downstream logic to explicitly handle missing keys rather than assuming all reads will succeed.

## Troubleshooting
- **Error: User ID is not available in this context.** Ensure the workflow is executing inside a standard Salt environment where salt_user_id is injected automatically. If you are calling this node from custom code or a non-standard context, adjust your setup so a valid user context is provided.
- **Key not found and workflow stops**: If a missing key raises a not-found or similar error and halts the workflow, change missing_key_behavior to "Return empty" or "Return default" and provide a meaningful default_value to allow the pipeline to continue gracefully.
- **Downstream JSON parsing failures**: When a JSON parser node fails on the data output, verify that the stored content is valid JSON and that it was written with a JSON serialization format. If the original value was plain text or another format, adjust downstream nodes to treat data as text or re-upload the object in JSON form.
- **Unexpected empty data**: If data is empty while you expect content, check that read_mode is set to "Value" and not "Metadata Only". Also confirm that missing_key_behavior is not returning an empty/default result because the key is actually missing.
- **Object not found in expected namespace**: When Storage Read cannot locate data you are sure was written, verify that the namespace input matches the namespace used at write time. Objects written under a custom namespace, such as "production" or "experiment-a", will not be visible when reading from "default".
