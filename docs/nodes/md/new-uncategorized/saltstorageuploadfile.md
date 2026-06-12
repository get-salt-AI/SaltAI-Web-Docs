# Storage Upload File

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node sets up a destination in persistent object storage so a user can upload a file directly from the canvas widget without running the full workflow. It validates the namespace and filename, infers the file MIME type from the filename extension, and returns the storage key, namespace, detected content type, and a download URL when available.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltstorageuploadfile.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want users to place files into persistent storage before or alongside a workflow, such as uploading PDFs for later parsing, dropping CSV reports into a shared namespace, or attaching reference assets that downstream steps should read. It typically appears near the start of a storage-based pipeline, where the upload widget is used first and the outputs are then passed into storage consumers. A common pattern is **Storage Upload File → Storage Read** so the uploaded object can be fetched or inspected later by key and namespace. It also works well with other storage-management nodes that list, delete, or organize persistent files by namespace. Best practice is to choose stable, descriptive namespaces like `uploads`, `reports`, or `contracts_2026`, and to keep filenames predictable so downstream nodes can reliably reference them. If you need persistence across workflow runs, use this node instead of execution-scoped memory nodes.

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
<tr><td style="word-wrap: break-word;">namespace</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Logical storage grouping for the uploaded file. If blank after trimming, it falls back to `default`. The value is validated and should only use simple filename-safe characters; avoid spaces and special path characters.</td><td style="word-wrap: break-word;">reports</td></tr>
<tr><td style="word-wrap: break-word;">storage_key</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The filename or object key to store in the selected namespace. This is usually auto-filled by the upload widget after a file is chosen. It must not be empty and must use a simple safe filename format.</td><td style="word-wrap: break-word;">q2_revenue_summary.pdf</td></tr>
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
<tr><td style="word-wrap: break-word;">storage_key</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The stored filename/key for the uploaded object. Downstream storage nodes can use this to reference the file.</td><td style="word-wrap: break-word;">q2_revenue_summary.pdf</td></tr>
<tr><td style="word-wrap: break-word;">namespace</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The namespace where the file is stored. Use it together with `storage_key` to locate the object later.</td><td style="word-wrap: break-word;">reports</td></tr>
<tr><td style="word-wrap: break-word;">content_type</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">MIME type inferred from the file extension in the storage key. If the extension is unknown, a generic binary MIME type is returned.</td><td style="word-wrap: break-word;">application/pdf</td></tr>
<tr><td style="word-wrap: break-word;">download_url</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A presigned download URL for the uploaded object when user context is available. This can be passed to downstream nodes or external consumers that need temporary direct access to the file.</td><td style="word-wrap: break-word;">https://storage.example.com/persistent/<user-id>/reports/q2_revenue_summary.pdf?<presigned-url></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node itself is lightweight because it mainly validates inputs and prepares metadata; the actual file upload happens through the UI widget directly to object storage.
- **Behavior**: This node does not upload the file during workflow execution. The upload is triggered from the node widget before or outside the run, and execution only returns the configured storage details.
- **Limitations**: `storage_key` cannot be empty. If no file has been selected in the upload widget, execution will fail until a valid filename/key is present.
- **Behavior**: MIME type is inferred only from the filename extension, not by inspecting file contents. Unknown extensions return `application/octet-stream`.
- **Limitations**: Namespace and storage key must use a restricted safe-character format. Special characters, path separators, and complex names may be rejected.
- **Behavior**: The download URL is temporary and may be blank if user context is unavailable or if URL generation fails. It should not be treated as permanent storage metadata.

## Troubleshooting
- **Common Error 1**: `storage_key must not be empty. Pick a file in the upload widget first.` — Select a file in the upload widget so the filename is populated before running the node.
- **Common Error 2**: `storage_key '...' contains invalid characters.` — Rename the file or manually set a simpler key using only letters, digits, underscores, hyphens, and dots.
- **Common Error 3**: `namespace '...' contains invalid characters.` — Replace spaces, slashes, or special characters in the namespace with a simple safe name such as `uploads` or `client_reports`.
- **Common Error 4**: Download URL output is empty — The file may still be validly configured, but user context was unavailable or the storage service could not generate a presigned URL. Use `storage_key` and `namespace` with a downstream storage node like `Storage Read` to access the file instead.
- **Common Error 5**: Upload widget appears but the file is not accessible later — Confirm the file was actually uploaded through the widget and that downstream nodes use the exact same `namespace` and `storage_key` values.
