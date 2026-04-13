# Load GCS File

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Reads a file from a Google Cloud Storage (GCS) path and returns its contents as a string. It validates that the supplied path is a properly formatted GCS URI and delegates the actual read to a backend service, returning the textual content that is retrieved.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/loadgcsfilenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to load the text contents of a file stored in GCS (such as PDB, JSON, TXT, or other text-based formats) into a Salt workflow. Provide a fully qualified GCS path starting with gs:// and connect the resulting string output to downstream nodes that parse, analyze, or transform text.

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
<tr><td style="word-wrap: break-word;">gcs_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Fully qualified GCS path to the file to read. Must start with gs:// and include the bucket name and the complete object path.</td><td style="word-wrap: break-word;">gs://my-bucket/scaffolds/template.pdb</td></tr>
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
<tr><td style="word-wrap: break-word;">file_content</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The full textual content of the file read from the specified GCS path, returned as a single string that may contain multiple lines.</td><td style="word-wrap: break-word;">ATOM      1  N   GLY A   1      11.104  13.207  12.851  1.00 20.00           N  ...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Note 1**: The node expects a valid GCS URI in the form gs://bucket/path/to/object and will fail if the format is invalid.
- **Note 2**: The node treats the file content as text. If the file is binary or not encoded as UTF-8/ASCII-compatible text, the returned content may be unreadable, corrupted, or appear empty.
- **Note 3**: The environment running this node must have permission to read the specified GCS bucket and object; missing or insufficient IAM permissions will cause failures.
- **Note 4**: The read operation is subject to a fixed timeout (approximately 120 seconds), so very large files or slow network conditions may result in timeout errors.
- **Note 5**: Large files will produce long strings; ensure that downstream nodes can handle the resulting payload size to avoid performance or memory issues.

## Troubleshooting
- **Invalid GCS path format error**: Ensure the path begins with `gs://` and includes a valid bucket name and full object path, such as `gs://my-bucket/path/file.txt`.
- **Empty or unexpectedly short content**: Verify that the target file is non-empty, confirm that the object path is correct, and ensure the file is textual rather than binary.
- **Permission denied or file not found**: Check that the bucket and object exist and that the executing environment or service account has read access via appropriate IAM roles.
- **Timeout while loading the file**: Reduce file size if possible, check network connectivity, and consider alternative approaches if the file is very large or frequently accessed.
- **Garbled characters or encoding issues**: Confirm that the file is encoded in UTF-8 or another compatible text encoding, and re-save or convert the file if necessary before loading.
