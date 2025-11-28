# PDB Visualization

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates a self-contained HTML page to visualize a protein structure from a provided PDB string. The node uploads the PDB content to a backend service to obtain a publicly accessible structure URL, then embeds it in a Mol* (molstar.org) iframe viewer with a full-screen layout.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbvisualizationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a protein structure in PDB format and want an immediate, shareable visualization. Typical workflow: load or generate a PDB string, pass it to this node, and use the returned HTML to display the interactive 3D viewer in a web panel or save it for external viewing.

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
<tr><td style="word-wrap: break-word;">pdb_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The full PDB file contents as a string. This should be a valid PDB-formatted text representing the protein structure.</td><td style="word-wrap: break-word;">HEADER    EXTRACELLULAR MATRIX                    01-JAN-00 ATOM      1  N   MET A   1      11.104  13.207   2.100  1.00 20.00           N</td></tr>
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
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A complete HTML document string containing a full-screen Mol* iframe viewer for the uploaded protein structure.</td><td style="word-wrap: break-word;"><!DOCTYPE html><html><head><title>Full Screen Protein Viewer</title>...<iframe src="https://molstar.org/viewer/?structure-url=...&structure-url-format=pdb&hide-controls=1&collapse-left-panel=1" allowfullscreen></iframe>...</html></td></tr>
</tbody>
</table>
</div>

## Important Notes
- The node relies on an external service to upload the PDB content and return a public structure URL; network connectivity and service availability are required.
- If the upload service returns a non-200 response or fails, the node raises an exception containing the status and error detail.
- The returned HTML is a full document with an embedded Mol* viewer configured to hide controls and collapse the left panel for a clean, full-screen view.
- Ensure the resulting structure URL is publicly accessible so the Mol* viewer can fetch and render the structure.
- Large PDB inputs may take longer to upload; the node uses an extended timeout for the upload request.

## Troubleshooting
- Upload failed with error status: Verify the backend upload service is reachable, credentials/config (if any) are set up, and the PDB string is valid. Retry after checking network stability.
- Viewer displays blank or fails to load: Confirm the generated structure URL is publicly accessible and correctly encoded. Check browser console for blocked requests or CORS issues.
- Malformed PDB input: Validate the PDB format. Incorrect or corrupted content may cause the upload service to reject the input or Mol* to fail rendering.
- Slow loading times: Large structures or slow networks can delay loading. Consider simplifying the structure or ensuring faster network access.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/pdbvisualizationnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

