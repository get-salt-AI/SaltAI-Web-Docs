# Output

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Finalizes and exports workflow results. Depending on the selected type, it can save image batches to files, package data into a ZIP, or simply emit a string value to the UI. Produces UI metadata referencing saved assets for downstream consumption.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/outputs/saltoutput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this as the final node in a workflow to persist results. Typical patterns: save a batch of images as PNG/JPEG, bundle text/JSON/images/audio/video into a ZIP for download, or output a plain string message.

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
<tr><td style="word-wrap: break-word;">output_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Specifies how to export the provided data. Options: STRING (emit string to UI), PNG/JPEG (save image tensor batch), ZIP (create a ZIP from supported inputs), Path (ZIP the given filesystem path).</td><td style="word-wrap: break-word;">PNG</td></tr>
<tr><td style="word-wrap: break-word;">output_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The content to export. Expected types depend on output_type (e.g., image tensor for PNG/JPEG, string/list/dict/tensor/bytes for ZIP, filesystem path for Path, string for STRING).</td><td style="word-wrap: break-word;">Tensor of images for PNG/JPEG, or a dict like {"key": "value"} for ZIP</td></tr>
</tbody>
</table>
</div>

## Outputs

No outputs

## Important Notes
- Only PNG and JPEG image saving is supported for image tensors. Selecting WEBP currently does not produce image files.
- When output_type is STRING, the node returns a string in UI results; no files are written.
- For ZIP: behavior depends on the data type of output_data. Image tensors become multiple PNGs zipped; a string becomes a single .txt; a list becomes multiple .txt files; a dict becomes a single .json; raw audio bytes can be exported as MP3 if configured as ZIP (Audio) by the system; video tensors may be encoded and zipped if ZIP (Video) is enabled by the system.
- For Path: only files or directories within allowed input/output/temp directories can be zipped; external locations are rejected.
- Saved files are organized under an auto-generated asset folder (UUID) inside the output directory. Filenames for image batches follow output_<id>_0000.ext, output_<id>_0001.ext, etc.
- The node emits UI metadata (salt_metadata and salt_output) describing saved assets, including subfolder and filenames. No pipeline outputs are produced.

## Troubleshooting
- Nothing is saved when selecting WEBP: Choose PNG or JPEG for image saving; WEBP is not currently implemented for file output.
- Type mismatch errors or empty results: Ensure output_data matches the selected output_type. For example, provide an image tensor for PNG/JPEG, a string/list/dict/tensor/bytes for ZIP, or a valid path string for Path.
- ZIP with Path fails: Verify the path exists and is located under an allowed directory (input/output/temp). External paths are not permitted.
- No permission or save failures: Check write permissions on the output directory and ensure sufficient disk space.
- ZIP results missing expected files: For image tensors, verify the tensor contains a batch dimension; each item in the batch becomes an image inside the ZIP.
- Audio/video zipping not available: Some ZIP subtypes (ZIP (Audio), ZIP (Video)) may be disabled in your environment’s UI options. Use general ZIP with supported inputs or switch to PNG/JPEG/STRING as appropriate.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/outputs/saltoutput/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

