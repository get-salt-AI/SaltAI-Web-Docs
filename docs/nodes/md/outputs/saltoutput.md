# Output

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node is the terminal output for a Salt workflow. It accepts arbitrary upstream data and, based on the selected output type, saves it as images, plain text, ZIP archives, or a zipped filesystem path. It also returns structured metadata that Salt uses to render results, previews, and downloadable files in the interface.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/outputs/saltoutput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Place the Output node at the end of any workflow that needs to surface results to the user or persist them as artifacts. Wire the last processing or model node into the `output_data` input, and configure `output_type` to match the data and desired packaging. In image workflows, connect an image tensor batch from a sampler or upscaler and choose `PNG` or `JPEG` to save all frames and get preview thumbnails. In text/LLM workflows, choose `STRING` to emit readable text directly, or `ZIP` to bundle larger outputs, lists of documents, or JSON into a single downloadable archive. For workflows that already produce files or folders on disk (for example, a directory of reports or generated assets), pass the path into `output_data` and use `Path` so the node will zip the folder (if it is under Salt’s allowed directories) into one artifact. The Output node is typically used together with `SaltInput` and `SaltDynamicInput` upstream, which define user-facing inputs and dynamic prompt text, while this node is the canonical sink that hands completed results back to Salt’s runtime and UI.

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
<tr><td style="word-wrap: break-word;">output_type</td><td>True</td><td style="word-wrap: break-word;">STRING (enum: STRING, PNG, JPEG, WEBP, ZIP, Path)</td><td style="word-wrap: break-word;">Controls how `output_data` is handled and stored. STRING: convert `output_data` to text and return it as a simple result without writing files. PNG or JPEG: interpret `output_data` as an image batch tensor, save each frame to the output directory with sequential filenames, and enable image previews. WEBP is available in the UI but, in this implementation, preview images are flagged only for PNG/JPEG. ZIP: depending on `output_data` type, create an archive of images (tensor -> PNG files), a single text file (string), multiple text files (list), or a single JSON file (dict). Path: treat `output_data` as an existing file or directory path and zip it, as long as it resides within configured input/output/temp directories.</td><td style="word-wrap: break-word;">PNG</td></tr>
<tr><td style="word-wrap: break-word;">output_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The data to be output. For PNG/JPEG, supply a tensor representing a batch of images (for example, the result of an image generation node). For STRING, any value that can be meaningfully converted to text is accepted (for example, an LLM response, a serialized report, or logs). For ZIP, behavior depends on the type: a tensor creates a ZIP of PNG images; a string is stored as one .txt file; a list is written as separate .txt files; a dict is serialized as a single .json file. For Path, this must be a valid existing file or directory path under an allowed Salt directory; the node zips that file or directory.</td><td style="word-wrap: break-word;">A batch image tensor from a diffusion model, or a long-form markdown report string like "# Weekly performance report..."</td></tr>
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
<tr><td style="word-wrap: break-word;">ui</td><td style="word-wrap: break-word;">dict</td><td style="word-wrap: break-word;">A metadata dictionary that describes all generated artifacts for Salt’s runtime and UI. It contains a `salt_metadata` list with entries such as `salt_id` (node ID), `salt_reference_uuid` (asset folder UUID), `salt_description`, `salt_asset` (whether the result is a file-based asset), and `salt_file_extension` (e.g. PNG or ZIP). The `salt_output` field holds either file descriptors (filename, subfolder, type) for saved files or raw strings for STRING outputs. For image outputs, an `images` array mirroring `salt_output` is added for preview rendering.</td><td style="word-wrap: break-word;">{ "ui": { "salt_metadata": [{ "salt_id": 7, "salt_reference_uuid": "c3eac56f-0e2f-4e27-9cc7-73db7c3bff1c", "salt_description": "", "salt_asset": true, "salt_file_extension": "PNG" }], "salt_output": [{ "filename": "output_7_0000.png", "subfolder": "c3eac56f-0e2f-4e27-9cc7-73db7c3bff1c", "type": "output" }], "images": [{ "filename": "output_7_0000.png", "subfolder": "c3eac56f-0e2f-4e27-9cc7-73db7c3bff1c", "type": "output" }] } }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Writing many images or large ZIPs can be I/O heavy; if you experience slow runs, reduce image batch size, avoid unnecessary ZIPs, or split workflows into smaller chunks.
- **Limitations**: PNG/JPEG saving only triggers when `output_data` is a compatible image tensor; if you pass a string, list, or other type with these formats selected, no images will be saved and the output will likely be limited to metadata or errors.
- **Behavior**: When `output_type` is STRING, the node does not create any files; it simply converts `output_data` to a string and returns it as part of `salt_output`, which is ideal for pure text workflows or debugging outputs.
- **Behavior**: For Path mode, the node validates that the given path exists and lies inside allowed directories (input, output, or temp). If these conditions are not met, it raises an error instead of accessing the filesystem, which can break the workflow if misconfigured.
- **Behavior**: Each execution creates or uses a per-asset subfolder (backed by a UUID) beneath the output directory so that separate runs do not overwrite each other’s files unless external cleanup or explicit reuse is implemented.

## Troubleshooting
- **Images not showing in result gallery**: If execution succeeds but no image previews appear, verify that `output_type` is set to PNG or JPEG and that `output_data` is a non-empty image tensor batch from the upstream node. Also confirm that the upstream node is not outputting file paths or plain strings instead of tensors.
- **Errors when using Path output**: Messages like "The path specified ... does not exist" or "not within the allowed directories" indicate an invalid or unsafe path. Check that the path is correct, that the file or directory exists by the time this node runs, and that the path resolves under the configured input, output, or temp directories.
- **Unexpected or empty ZIP contents**: If your ZIP is created but missing expected files, check the type of `output_data`. For image ZIPs it must be a tensor; for lists, ensure the list is populated; for dicts, confirm it is serializable to JSON. When passing strings, remember that only a single .txt file will be created inside the ZIP.
- **Unreadable STRING output**: If STRING output shows low-level representations (for example, an object address) instead of readable text, it means the value was just stringified. Pre-format complex structures (by serializing to JSON or building a text template) upstream so that the resulting string is meaningful.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/outputs/saltoutput/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

