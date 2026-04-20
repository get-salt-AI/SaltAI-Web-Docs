# File

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node lets you choose from files uploaded to the current workflow and returns both the resolved file path and its UTF‑8 text contents when the file appears to be text-like. For common binary formats such as images, office documents, and archives, it intentionally skips text extraction and returns an empty string for the text output. This makes it a flexible bridge between user-uploaded assets and downstream nodes that either need a path or raw text.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/saltfileinput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use `SaltFileInput` whenever your workflow needs to start from or incorporate a user-uploaded file. Typical scenarios include: reading `.txt`, `.md`, `.json`, or `.csv` files into text-processing or LLM nodes; selecting images, PDFs, or other binary assets to send to specialized parsing or analysis nodes via their file path; and reusing a workflow with different uploaded files by choosing a different entry from the dropdown. Place this node near the beginning of your workflow as an entry point for external data, since it reads directly from the workflow’s configured input directory and does not depend on upstream nodes. Downstream, connect `file_path` to nodes that expect a path to a file on disk (such as custom parsers or media processors) and connect `text_contents` to nodes expecting text (such as summarizers, classifiers, or prompt-building nodes). Combine it with branching or validation logic (for example, based on file extension) to route different file types to appropriate processing paths, using `file_path` for binary or complex document formats and `text_contents` for UTF‑8 text files.

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
<tr><td style="word-wrap: break-word;">file</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Name of a file available in this workflow's input directory. Shown as a sorted dropdown list of existing files and also supports direct upload via the same widget. The value must correspond to a valid annotated file; otherwise input validation fails with an error.</td><td style="word-wrap: break-word;">customer_feedback_march_2026.txt</td></tr>
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
<tr><td style="word-wrap: break-word;">file_path</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Resolved filesystem path to the selected file. Use this when downstream nodes need to access and read the file themselves, such as image or document processors, custom parsers, or storage nodes. The path is guaranteed to point to an existing annotated file if validation passes.</td><td style="word-wrap: break-word;">/data/workflows/42/inputs/customer_feedback_march_2026.txt</td></tr>
<tr><td style="word-wrap: break-word;">text_contents</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">UTF‑8 decoded contents of the file when its extension is not in the internal blocklist of binary formats (images, office documents, archives, etc.). For blocklisted file types or when reading/decoding fails, this output is an empty string. Intended mainly for plain-text files such as `.txt`, `.md`, `.json`, or `.csv` encoded in UTF‑8.</td><td style="word-wrap: break-word;">Customer ID: 18372 Feedback: The onboarding flow is confusing around step 3. Please add clearer instructions. Rating: 3 </td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node loads the entire contents of non-blocklisted files into memory and decodes them as UTF‑8; very large text files may increase memory usage and slow execution, so consider pre-splitting or chunking large documents.
- **Limitations**: A fixed blocklist of common binary extensions (.png, .jpg, .jpeg, .gif, .webp, .heic, .heif, .hevc, .avif, .tiff, .bmp, .ico, .svg, .pdf, .doc, .docx, .xls, .xlsx, .ppt, .pptx, .zip, .gzip, .7z) is never read for text, so `text_contents` will be empty for these; for such formats, rely on `file_path` and specialized parser or converter nodes.
- **Behavior**: For all non-blocklisted file types, the node reads the file in binary mode and decodes it as UTF‑8. If decoding fails (for example, if the file uses a different encoding or contains binary data), it logs an error and leaves `text_contents` as an empty string without interrupting the workflow.
- **Behavior**: Input validation checks that the specified `file` matches an existing annotated file. If not, the node reports a validation error (`Invalid file path: <file>`) so issues are detected before runtime processing.
- **Behavior**: The list of selectable files is derived from the environment’s configured input directory; files must be uploaded through the platform’s UI or otherwise placed in that directory to appear in the dropdown.

## Troubleshooting
- **Invalid file path error**: If you see `Invalid file path: <file>`, the selected name does not match any known annotated file. Re-upload the file via the workflow UI or pick a filename from the dropdown instead of typing it manually.
- **Empty text_contents for expected text file**: When `text_contents` is empty but you expect text, first confirm the extension is not in the binary blocklist. If it is safe, verify the file is encoded in UTF‑8; if not, re-save it as UTF‑8 (for example as `.txt` or `.md`) and upload again.
- **Corrupted or unreadable characters**: If the text output is garbled, the file is likely using a different encoding (such as ISO‑8859‑1 or UTF‑16). Convert the file to UTF‑8 with an external tool and re-upload, or use a custom reader node that supports that encoding.
- **Downstream node errors on content**: If a downstream parser or model fails, verify that you connected the appropriate output: `file_path` for nodes that read files directly, and `text_contents` for nodes that expect raw text. Also ensure the file format and contents match what the downstream node expects (for example, valid JSON for a JSON parser).

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/inputs/saltfileinput/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

