# File

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Reads a file that has been uploaded to the current workflow and provides both its absolute path and, when applicable, its UTF-8 text contents. For non-text or binary file types, the text output is suppressed to avoid unreadable data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/saltfileinput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a workflow when you need to select a previously uploaded file. Route the file path to nodes that consume files (e.g., image/audio processors) and use the text output for text-processing steps when the file is a readable text format.

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
<tr><td style="word-wrap: break-word;">file</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Select a file from the workflow's uploaded files list. The UI also allows uploading a new file for selection.</td><td style="word-wrap: break-word;">notes.txt</td></tr>
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
<tr><td style="word-wrap: break-word;">file_path</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Absolute path to the selected file. Use this for downstream nodes that need to read the file directly.</td><td style="word-wrap: break-word;">/workspace/inputs/notes.txt</td></tr>
<tr><td style="word-wrap: break-word;">text_contents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">UTF-8 decoded contents of the file if it is a text-readable type; empty for many binary formats or if decoding fails.</td><td style="word-wrap: break-word;">Meeting notes for Q3...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Text output behavior**: The node attempts to read text using UTF-8. If the file extension indicates a likely binary/non-text file, the text output is left empty to avoid unreadable content.
- **Blocklisted file types for text extraction**: .png, .jpg, .jpeg, .gif, .webp, .heic, .heif, .hevc, .avif, .tiff, .bmp, .ico, .svg, .pdf, .doc, .docx, .xls, .xlsx, .ppt, .pptx, .zip, .gzip, .7z
- **File source**: Only files present in the workflow's input directory (including those uploaded via the UI) will be listed.
- **Encoding expectation**: Text extraction assumes UTF-8 encoding. Non-UTF-8 text files may result in empty or garbled text output.
- **Downstream usage**: Prefer the file_path output for non-text processing (e.g., image/audio/video) and use text_contents only when the file is known to be text-readable.

## Troubleshooting
- **File not listed**: Ensure the file is uploaded to the current workflow or placed in the input directory. Refresh the file list if needed.
- **Empty text output for a text file**: The file may be on the blocklist or not UTF-8 encoded. Convert the file to UTF-8 or rename with an appropriate extension and retry.
- **Unreadable characters in text_contents**: The file likely uses a different encoding. Convert to UTF-8 and re-upload.
- **Errors reading file**: Confirm the file path is valid and the file is accessible. Re-upload the file if it may be corrupted.
- **Binary file text expected**: For formats like images, PDFs, or Office documents, the node does not extract text. Use a specialized OCR or document parsing node.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/inputs/saltfileinput/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

