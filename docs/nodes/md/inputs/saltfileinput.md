# File

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Reads a file that has been uploaded to the workflow and returns its full path along with its UTF-8 text contents. For files commonly considered binary (images, office docs, archives, etc.), only the file path is output and text contents are suppressed. Useful for passing a file path downstream and optionally extracting human-readable text for text-friendly formats.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/saltfileinput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a workflow when you need to reference an uploaded file by path and, if applicable, read its UTF-8 text contents. Typical flows include routing the file path to processors that accept files and sending the text contents to text-processing nodes when the file is a text-based format.

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
<tr><td style="word-wrap: break-word;">file</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Select a file from the workflow's uploaded files directory. The selection list is populated from files you have uploaded to this workflow.</td><td style="word-wrap: break-word;">notes/session_plan.txt</td></tr>
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
<tr><td style="word-wrap: break-word;">file_path</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Absolute or annotated path to the selected file for use by downstream nodes.</td><td style="word-wrap: break-word;">/input/notes/session_plan.txt</td></tr>
<tr><td style="word-wrap: break-word;">text_contents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">UTF-8 decoded contents of the file. For non-text or blocked file types, this may be empty or not human-readable.</td><td style="word-wrap: break-word;">Agenda:\n1) Introductions\n2) Roadmap review\n3) Action items</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node suppresses text extraction for common binary file types such as images, PDFs, Office documents, and archives. For these, text_contents may be empty.
- Text is decoded as UTF-8. Files in other encodings may fail to decode, resulting in empty or partial contents.
- You must upload the file to the workflow's input directory before it will appear in the selection list.
- The file path is still returned even when text extraction is suppressed, allowing downstream file-processing nodes to consume it.
- Validation ensures the selected file exists; invalid selections will fail validation.

## Troubleshooting
- Selected file not listed: Ensure the file has been uploaded to this workflow and resides in the workflow's input directory.
- Invalid file path error: The file may have been removed or renamed after selection. Re-upload or reselect the file.
- Empty text_contents: The file may be a binary or blocked type (e.g., .png, .pdf, .docx) or not valid UTF-8. Use a text-based format (e.g., .txt, .json) or process the file differently.
- Garbled characters in text_contents: The file encoding may not be UTF-8. Convert the file to UTF-8 and try again.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/inputs/saltfileinput/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

