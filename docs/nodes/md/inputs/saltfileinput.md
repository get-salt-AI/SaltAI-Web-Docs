# File

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Reads a file that was uploaded to the workflow and outputs its full path and, when applicable, its UTF-8 text contents. Non-text and certain binary file types are blocked from content extraction to avoid unreadable output; for these, only the path is provided.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/saltfileinput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the beginning of a workflow when you need to reference an uploaded file or extract its text content for downstream processing. Select a file from the workflow's upload directory; pass the file_path output to nodes that operate on files (images, PDFs, etc.) and use text_contents for nodes that consume text.

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
<tr><td style="word-wrap: break-word;">file</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Select a file from the workflow's input directory. You can also upload a new file via the selector.</td><td style="word-wrap: break-word;">notes.txt</td></tr>
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
<tr><td style="word-wrap: break-word;">file_path</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Absolute path to the selected file for use by other nodes.</td><td style="word-wrap: break-word;">/inputs/notes.txt</td></tr>
<tr><td style="word-wrap: break-word;">text_contents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">UTF-8 decoded text contents of the file when readable; empty string for blocked or non-text files.</td><td style="word-wrap: break-word;">Meeting notes for Q2...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Content extraction blocklist**: Text extraction is skipped for these extensions: .png, .jpg, .jpeg, .gif, .webp, .heic, .heif, .hevc, .avif, .tiff, .bmp, .ico, .svg, .pdf, .doc, .docx, .xls, .xlsx, .ppt, .pptx, .zip, .gzip, .7z.
- **Encoding**: Files are read as binary and decoded as UTF-8. If decoding fails, text_contents will remain empty and an error is logged.
- **Path validity**: The node validates that the selected file exists in the annotated input directory before running.
- **Best practice**: Use file_path for non-text file processing (e.g., images or archives) and text_contents only for plain-text compatible files.

## Troubleshooting
- **Invalid file path**: If you see 'Invalid file path: <filename>', ensure the file is present in the workflow's input directory or re-upload it via the selector.
- **Empty text_contents for text file**: The file may not be UTF-8 encoded or may be on the blocklist. Convert the file to UTF-8 or use a different node that supports the file type.
- **Garbled text**: This indicates a non-UTF-8 encoding. Convert the file to UTF-8 before uploading.
- **No files listed**: Verify that the input directory contains files and that you have permission to read them.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/inputs/saltfileinput/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

