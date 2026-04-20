# Load Cloud File

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node retrieves the contents of a file stored at a specified Google Cloud Storage (GCS) path and outputs it as a single string. It validates that the input path is a correctly formatted GCS URI before delegating the read operation to an external biotech CPU service. The node is intended for workflows that need to pull text-based files (such as PDB structures or configuration files) from GCS into Salt pipelines.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/loadgcsfilenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever your pipeline needs to load a single file from a GCS bucket for further processing, particularly in biotech or structural biology workflows. It is typically positioned near the start of a pipeline, after user or system selection of a GCS path and before nodes that parse, analyze, or transform the file contents. Example scenarios include: loading a PDB scaffold from a bucket path (for example, before passing it into scaffold-processing or protein-design nodes); loading a JSON or text configuration file that controls downstream model parameters; or centralizing shared reference files in a GCS bucket for multiple teams and workflows. Upstream, the `gcs_path` may be provided directly by the user or via a configuration/parameter node. Downstream, nodes that parse PDB, FASTA, or JSON can consume the `file_content` string. For best results, ensure the GCS URI is fully qualified in the `gs://bucket/path` format, the underlying biotech CPU service has read permissions on the bucket, and files are of a reasonable size so they can be loaded into memory as a string without performance issues.

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
<tr><td style="word-wrap: break-word;">gcs_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The fully qualified Google Cloud Storage URI of the file to be loaded. This must start with `gs://` and follow the format `gs://bucket-name/path/to/object.ext`. The path is trimmed internally, but providing leading or trailing whitespace can still cause confusion if the effective path is wrong. The referenced file should be text-based or safely representable as a string; extremely large objects may not be appropriate due to memory and timeout constraints.</td><td style="word-wrap: break-word;">gs://emi-file-uploads/scaffolds/1abc_template.pdb</td></tr>
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
<tr><td style="word-wrap: break-word;">file_content</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The complete content of the specified GCS file, returned as a single string. This may represent a PDB file, JSON configuration, sequence file, or any other text-based format stored in GCS. Downstream nodes can parse or process this string according to the expected file format.</td><td style="word-wrap: break-word;">HEADER    OXIDOREDUCTASE                          01-JAN-24   1ABC TITLE     SAMPLE PDB STRUCTURE FOR SCAFFOLD DESIGN ATOM      1  N   MET A   1      11.104  13.207   2.345  1.00 30.00           N ATOM      2  CA  MET A   1      12.345  13.800   2.900  1.00 30.00           C END </td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The entire file is loaded into memory as a string; large files may result in high memory usage and increased latency and can approach the node's timeout limit.
- **Limitations**: The `gcs_path` must start with `gs://` and follow the `gs://bucket/path` pattern. Any other format results in an immediate validation error before contacting backend services.
- **Behavior**: If the backend service returns a response without a `content` field, the node outputs an empty string. Downstream logic should explicitly handle the possibility of empty content.
- **Behavior**: File access is delegated to an external biotech CPU service that must have the appropriate IAM permissions and network connectivity to reach the specified GCS bucket and object.

## Troubleshooting
- **Invalid GCS path format**: If you encounter an error like `Invalid GCS path format. Expected gs://bucket/path, got: <value>`, verify that the input starts with `gs://`, includes the bucket name and object path, and does not contain unintended whitespace or typos.
- **Empty or unexpected file_content**: When `file_content` is empty or shorter than expected, confirm that the object exists in GCS, is non-empty, and that the path is correct (including folder prefixes and extension). Remove any trailing spaces from the URI and ensure you are pointing at the correct object.
- **Permission or backend errors**: Errors indicating permission denied, missing bucket, or failures from the biotech CPU service usually mean the underlying service cannot access GCS. Check that the service account has read permissions for the bucket and object, and that the bucket's region and access controls are compatible.
- **Timeouts for large files**: The node uses a fixed timeout (120 seconds) for the backend read operation. For very large files or slow networks, you may hit this timeout. Reduce file size, split the data across multiple smaller files, or pre-process and store lighter-weight artifacts to keep read times within the timeout window.
