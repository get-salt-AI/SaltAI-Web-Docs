# Storage Write

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Storage Write saves text and structured data into persistent Salt storage keyed by user ID, namespace, and a logical key path. It serializes strings as plain text and dicts, lists, numbers, and booleans as JSON, attaching optional metadata tags for later querying or auditing. The node also enforces key validation and supports conditional overwrite to prevent accidental data loss.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/memory-storage/saltstoragewrite.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to persist data across workflow runs, sessions, or branches in Salt. Typical scenarios include saving model outputs (summaries, chat transcripts, classification results), caching expensive computation results, or checkpointing intermediate pipeline state so later runs can resume or reuse them. It generally appears downstream of processing nodes that generate text or structured outputs (for example, LLM response nodes, extraction or parsing nodes, aggregation nodes) and upstream of nodes that later read back stored values (such as a "SaltStorageRead" node or custom retrieval logic). Combine it with upstream nodes that produce dictionaries or lists (for JSON storage) and with downstream analytics or retrieval nodes that use the returned key and metadata for subsequent lookups. Use different "namespace" values per project or workflow to avoid key collisions and keep your storage logically organized. For safe checkpointing patterns, disable "overwrite" so the node will fail rather than silently overwrite an existing value, and handle that error in your orchestration. Attach descriptive "metadata_tags" (like model version, stage, tenant, or feature flags) to make stored records easier to audit, filter, and debug later.

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
<tr><td style="word-wrap: break-word;">key_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Logical key used to identify this stored object within a user plus namespace. Must satisfy the storage-key validation (typically non-empty; use dots or hyphens for hierarchy and avoid unsupported characters). This is the primary lookup key you will reuse when reading the data back.</td><td style="word-wrap: break-word;">projects.news_summarizer.runs.2024_04_20.final_summary</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The content to persist. Supports text and JSON-serializable structures only: strings, dictionaries, lists, integers, floats, and booleans. Strings are stored as plain text; non-string primitives and collections are serialized as JSON. Raw bytes are not accepted; convert binary data, for example images, to a text-safe representation like base64 or a URL before storing.</td><td style="word-wrap: break-word;">{'article_id': 'nyt-2024-04-20-12345', 'summary': 'The central bank signaled it will keep interest rates unchanged through Q3.', 'model': 'gpt-4.1-mini', 'created_at': '2024-04-20T11:32:00Z'}</td></tr>
<tr><td style="word-wrap: break-word;">namespace</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional logical namespace used to group keys per workflow, project, or environment. Leading and trailing whitespace are stripped; if the final value is empty, it falls back to "default". Use this to separate different applications, for example "prod", "staging", or "experiments", or different feature domains while reusing similar key patterns.</td><td style="word-wrap: break-word;">news_summarizer_prod</td></tr>
<tr><td style="word-wrap: break-word;">overwrite</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls whether an existing key may be overwritten. When true, any existing value at this key_path in the namespace is replaced. When false, a write attempt to an already-existing key results in a write conflict error and the node fails, which is useful to ensure idempotent or one-time writes.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">metadata_tags</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON object encoded as a string containing custom metadata tags to attach to the stored object. Keys and values should be strings, and the value must be valid JSON or the node will raise an error. These tags can include attributes like model, stage, tenant, or business-specific labels used for querying or auditing stored records.</td><td style="word-wrap: break-word;">{"model": "gpt-4.1-mini", "stage": "final", "tenant": "acme_corp"}</td></tr>
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
<tr><td style="word-wrap: break-word;">key_path</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The final validated key path under which the data was stored. This is identical to the possibly normalized input key_path and can be passed downstream to read or reference this object later.</td><td style="word-wrap: break-word;">projects.news_summarizer.runs.2024_04_20.final_summary</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The stored data in string form, safe for downstream text-based nodes. If the input was a string, it is returned unchanged; otherwise, it is the UTF-8 decoded representation of the serialized payload, either plain text or JSON.</td><td style="word-wrap: break-word;">{"article_id": "nyt-2024-04-20-12345", "summary": "The central bank signaled it will keep interest rates unchanged through Q3.", "model": "gpt-4.1-mini", "created_at": "2024-04-20T11:32:00Z"}</td></tr>
<tr><td style="word-wrap: break-word;">metadata</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON string containing metadata returned by the underlying storage client about the stored object. This typically includes fields like stored size, timestamps, content_type, serialization_format, and any custom metadata_tags that were provided.</td><td style="word-wrap: break-word;">{"key_path": "projects.news_summarizer.runs.2024_04_20.final_summary", "namespace": "news_summarizer_prod", "content_type": "application/json", "serialization_format": "json", "size_bytes": 324, "created_at": "2024-04-20T11:32:06.123Z", "metadata": {"model": "gpt-4.1-mini", "stage": "final", "tenant": "acme_corp"}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Large payloads can hit storage size limits; oversized writes raise a "Payload too large" error. Consider storing heavy binary assets like images or videos elsewhere and only persisting references here.
- **Limitations**: Raw binary bytes are not supported; all data must be convertible to text or JSON. If you need to store binary content, encode it, for example as base64 or as a signed URL, before passing it to this node.
- **Behavior**: When overwrite is disabled and the key already exists, the node fails with a specific write conflict error rather than silently overwriting the existing value. This is intended for safe checkpoint and de-duplication patterns.
- **Behavior**: If metadata_tags is not valid JSON, the node fails during parsing. Always pass a well-formed JSON object string, using double quotes around keys and values.
- **Behavior**: The node requires a valid salt_user_id from the execution context; without it, it raises an error and does not attempt to write, ensuring all data remains user-scoped.

## Troubleshooting
- **Write conflict: key already exists and overwrite is disabled.**: This indicates that the specified key_path and namespace already contain a record and overwrite is set to false. Either enable overwrite, change the key_path, for example by including a timestamp or unique ID, or clear the existing record if it is safe to delete.
- **Payload too large for storage limits.**: The data exceeds the maximum allowed size for a single object in storage. Reduce the size of the payload by truncating text, splitting data into multiple keys, or storing heavy content like images or PDFs externally and persisting only references or summaries.
- **User ID is not available in this context.**: The runtime did not provide salt_user_id, so the node cannot determine which user bucket to store data in. Ensure the node runs inside a proper Salt workflow execution context where user identity is available, and avoid manually invoking it without the platform's orchestration.
- **Storage Write failed: <details>.**: A generic error wrapper for unexpected exceptions such as network issues, service outages, or validation errors. Inspect the error message for hints, verify that key_path passes validation rules, ensure metadata_tags is valid JSON, and check connectivity or configuration of the underlying storage service.
