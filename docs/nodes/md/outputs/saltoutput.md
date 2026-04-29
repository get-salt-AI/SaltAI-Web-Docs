# Output

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node is the required final output node in a Salt workflow. It receives data from upstream nodes and, based on the selected output type, writes images or archives to Salt’s output directory or returns a simple string result. It also emits structured metadata so the Salt platform can display previews and provide download links.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/outputs/saltoutput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Place this node at the end of any workflow where you need to expose or persist the final result. Connect the main data you want to return to `output_data` and choose an `output_type` that matches the data. For image pipelines (e.g., generation, editing, upscaling), send a batch image tensor to `output_data` and set `output_type` to `PNG` or `JPEG` so each image in the batch is saved as a file. For text-centric workflows, set `output_type` to `STRING` and pass the final text response; nothing is written to disk, and the string is returned for display or API consumption. When you need a single downloadable artifact containing multiple items—such as many images, multiple text documents, or a JSON report—use `output_type = 'ZIP'` and pass a tensor, list, string, or dict; the node will serialize these into an archive. If an upstream step already created files or directories and you just want to package them, use `output_type = 'Path'` with a path under Salt’s input/output/temp directories. A typical pattern is: `SaltInput` → processing/model nodes → `SaltOutput` with an appropriate `output_type`. Workflows are validated to contain at least one `SaltOutput` so results can be surfaced to users.

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
<tr><td style="word-wrap: break-word;">output_type</td><td>True</td><td style="word-wrap: break-word;">STRING (enum: STRING, PNG, JPEG, WEBP, ZIP, Path)</td><td style="word-wrap: break-word;">Specifies how to treat and persist `output_data`. `STRING` returns the data as plain text without creating files. `PNG` and `JPEG` expect an image tensor batch and save each item as an image file. `WEBP` is treated as an image extension for previews in compatible flows. `ZIP` creates an archive, supporting image tensors (saved as PNGs inside), strings (single .txt), lists (multiple .txt files), or dicts (one .json). `Path` zips an existing file or directory, as long as it resides inside Salt’s allowed input/output/temp directories.</td><td style="word-wrap: break-word;">PNG</td></tr>
<tr><td style="word-wrap: break-word;">output_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to export. For `PNG`/`JPEG`, this must be a torch tensor batch of images (e.g. shape [N, C, H, W]) from an upstream image node. For `STRING`, any value that can be meaningfully cast to text, such as a generated answer or report. For `ZIP`, use a tensor for images, a string for a single text document, a list of values to create multiple .txt files, or a dict to be serialized as one .json file. For `Path`, provide a filesystem path string pointing to a file or directory within Salt’s managed input/output/temp folders.</td><td style="word-wrap: break-word;">A tensor batch of 3 marketing banner images, or a dict like {"customer_id": 42, "summary": "High upsell potential"}</td></tr>
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
<tr><td style="word-wrap: break-word;">ui</td><td style="word-wrap: break-word;">DICT</td><td style="word-wrap: break-word;">A dictionary with UI-facing metadata and file references, under the key `ui`. It includes `salt_metadata` (list with fields such as `salt_id`, a generated `salt_reference_uuid`, description, asset flag, and file extension), `salt_output` (a list of file descriptors for saved artifacts or the string result when `output_type` is STRING), and, for image formats, an `images` list mirroring the file descriptors to support previews. File descriptors contain `filename`, `subfolder` (relative folder under the Salt output directory), and `type` set to `output`.</td><td style="word-wrap: break-word;">{'ui': {'salt_metadata': [{'salt_id': 7, 'salt_reference_uuid': 'b3c5f1d8-4f9f-4e2c-8e53-df1c29ce2af7', 'salt_description': '', 'salt_asset': True, 'salt_file_extension': 'PNG'}], 'salt_output': [{'filename': 'output_7_0000.png', 'subfolder': 'b3c5f1d8-4f9f-4e2c-8e53-df1c29ce2af7', 'type': 'output'}], 'images': [{'filename': 'output_7_0000.png', 'subfolder': 'b3c5f1d8-4f9f-4e2c-8e53-df1c29ce2af7', 'type': 'output'}]}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Writing large batches of high-resolution images or many items into a ZIP archive can significantly increase run time and disk IO; design workflows to avoid unnecessarily huge batches.
- **Limitations**: The `PNG` and `JPEG` modes assume `output_data` is an image tensor batch; providing other data types will not yield valid image files, and you may instead get a minimal or no-file output.
- **Limitations**: When using `output_type = 'Path'`, the given path must both exist and be under Salt’s configured input, output, or temp directories; external or absolute paths outside these roots will cause errors.
- **Behavior**: All file-based exports are stored inside a newly generated UUID subfolder under the output directory, and this UUID is returned as `salt_reference_uuid` so the platform can locate and serve assets.
- **Behavior**: For `STRING` outputs, the node does not touch the filesystem; `salt_output` simply contains the string representation, which is ideal for chat responses, logs, or textual analytics.

## Troubleshooting
- **Invalid directory error**: If you see a message indicating that a path is not within the allowed directories when using `Path`, move or create your files inside Salt’s input, output, or temp folders and update the path accordingly.
- **Nonexistent path error**: The message that the specified path does not exist means the upstream node never created the file or there is a typo in the path. Verify the actual on-disk location and correct the `output_data` value.
- **Missing files or empty ZIP**: If no images or archive contents appear, confirm that `output_type` matches the shape and type of `output_data` (tensor for `PNG`/`JPEG`, list/dict/string for `ZIP`); mismatched combinations lead to empty or unexpected results.
- **No image preview in UI**: If the Salt UI does not show thumbnails, ensure `output_type` is `PNG`, `JPEG`, or `WEBP` and that at least one file descriptor is present in `salt_output`; ZIP and Path outputs only provide archive downloads, not image previews.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/outputs/saltoutput/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

