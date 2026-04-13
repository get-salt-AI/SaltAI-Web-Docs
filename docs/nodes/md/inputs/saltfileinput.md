# File

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Reads a file that was uploaded to the workflow and outputs its absolute file path and, when possible, its UTF-8 text contents. For non-text and certain binary file types, content extraction is skipped and only the file path is provided.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/saltfileinput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the beginning of a workflow when you need to reference an uploaded file or extract its text content for further processing. Select a file from the workflow’s input directory, then pass file_path to nodes that operate on files (e.g., image or document processors) and text_contents to nodes that consume plain text.

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
<tr><td style="word-wrap: break-word;">file</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of a file in the workflow’s input directory, chosen from a file selector. You can also upload a new file via this selector before running the workflow.</td><td style="word-wrap: break-word;">notes.txt</td></tr>
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
<tr><td style="word-wrap: break-word;">file_path</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Absolute path to the selected file, suitable for use by downstream nodes that operate on files.</td><td style="word-wrap: break-word;">/inputs/notes.txt</td></tr>
<tr><td style="word-wrap: break-word;">text_contents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">UTF-8 decoded text contents of the file when it is readable as text and not on the blocklist. For blocked or non-text files, this will be an empty string.</td><td style="word-wrap: break-word;">Meeting notes for Q2...</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Content extraction blocklist**: Text extraction is skipped for files with these extensions: .png, .jpg, .jpeg, .gif, .webp, .heic, .heif, .hevc, .avif, .tiff, .bmp, .ico, .svg, .pdf, .doc, .docx, .xls, .xlsx, .ppt, .pptx, .zip, .gzip, .7z.
- **Encoding behavior**: Files are read as binary and decoded as UTF-8. If decoding fails, text_contents remains empty and the failure is logged internally rather than returning garbled output.
- **Path validation**: The node verifies that the selected file exists in the workflow’s designated input directory before executing; invalid or missing files cause the node to fail early.
- **Usage guidance**: Use file_path for non-text file workflows (images, archives, office documents) and reserve text_contents for plain-text or other UTF-8 compatible files.
- **Empty text for binary files**: For binary or blocked file types, the node intentionally leaves text_contents empty to avoid unreadable or misleading results.

## Troubleshooting
- **Invalid file path error**: If you encounter an error like 'Invalid file path: <filename>', confirm that the file is present in the workflow’s input directory and re-upload or reselect it in the node configuration.
- **Empty text_contents for a text-looking file**: The file may not be UTF-8 encoded or might be using a blocked extension. Convert the file to UTF-8 with an allowed extension and upload it again, or choose a node that supports that file type for content extraction.
- **Garbled or strange characters in output**: This typically indicates a non-UTF-8 encoding. Re-save the file explicitly as UTF-8 in your editor or conversion tool before uploading.
- **No files available in selector**: Ensure that the workflow’s input directory actually contains files and that you have read permissions. Try uploading a file again through the UI to repopulate the list.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/inputs/saltfileinput/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

