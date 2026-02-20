# Load GCS File

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Reads a file from a Google Cloud Storage (GCS) path and returns its contents as a string. Validates the path format and delegates the read operation to a backend service, returning whatever text content is retrieved.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/loadgcsfilenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to load the textual contents of a file stored in GCS into your workflow (e.g., PDB files, JSON, TXT). Provide a fully qualified GCS path starting with gs:// and connect the output to downstream nodes that accept string content.

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
<tr><td style="word-wrap: break-word;">gcs_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Fully qualified GCS path to the file to read. Must start with gs:// and include the bucket and object path.</td><td style="word-wrap: break-word;">gs://emi-file-uploads/scaffolds/template.pdb</td></tr>
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
<tr><td style="word-wrap: break-word;">file_content</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The complete textual content of the file loaded from the specified GCS path.</td><td style="word-wrap: break-word;">ATOM      1  N   GLY A   1      11.104  13.207  12.851  1.00 20.00           N  ...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node expects a valid GCS URI format (gs://bucket/path/to/object). It will raise an error if the format is invalid.
- The output is a string. If the target file is binary or not UTF-8/ASCII text, the returned content may be unreadable or empty.
- Access to the specified GCS bucket/object must be permitted for the underlying service; insufficient permissions will cause failures.
- The operation has a fixed timeout (approximately 120 seconds). Very large files or slow networks may lead to timeouts.
- If the file is large, consider whether downstream nodes can handle long strings or large payloads.

## Troubleshooting
- Invalid GCS path format error: Ensure the path begins with gs:// and includes the bucket and full object path (e.g., gs://my-bucket/path/file.txt).
- Empty content returned: Verify the file actually contains text, confirm it is not binary, and check that the specified object path is correct.
- Permission denied or not found: Confirm that the service has read access to the bucket and object; verify IAM roles and that the object exists.
- Timeout while loading: Reduce file size, check network connectivity, or try again later. If the file is very large, consider alternative approaches to handle or stream the data.
- Unexpected characters or encoding issues: Ensure the file is encoded in UTF-8 or a compatible text encoding expected by downstream consumers.
