# PDB Visualization

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates an embeddable HTML viewer for a protein structure from PDB content. It uploads the provided PDB text to secure storage and returns a full-page HTML string that loads the structure in a Mol* (molstar.org) viewer. If a batch of PDBs is provided, only the first entry is visualized.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbvisualizationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to interactively inspect protein structures produced or loaded in a workflow. Connect a PDB-type output that provides a dictionary of {id: pdb_text} to this node, then route the resulting HTML string to a display or output node to show an interactive 3D viewer in the app.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Protein structure(s) to visualize. Expects a dictionary mapping an identifier string to its PDB text content. If multiple entries are provided, only the first one found is uploaded and visualized in the Mol* viewer.</td><td style="word-wrap: break-word;">{"proteinA": "ATOM      1  N   MET A   1      11.104  13.207  -2.222  1.00 20.00           N \n...\nEND"}</td></tr>
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
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML document as a string that embeds a full-page Mol* viewer iframe pointing to the uploaded PDB structure via a signed URL. Suitable for rendering directly in a browser or passing to an output/display node.</td><td style="word-wrap: break-word;"><!DOCTYPE html><html><head><title>Full Screen Protein Viewer</title>...</head><body><iframe src="https://molstar.org/viewer/?structure-url=<encoded-signed-url>&structure-url-format=pdb&hide-controls=1&collapse-left-panel=1" allowfullscreen></iframe></body></html></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Note 1**: Only the first structure in the input dictionary or batch is visualized; additional entries are ignored.
- **Note 2**: The node uploads the selected PDB text to secure storage and uses a time-limited signed URL to load it into an external Mol* (molstar.org) viewer.
- **Note 3**: If the node cannot extract a valid PDB from the input, it returns the literal string "None" instead of HTML.
- **Note 4**: Viewer controls are minimized by default (controls hidden, left panel collapsed) to provide a clean, full-page embedding.
- **Note 5**: The node is given elevated execution priority so previews appear promptly when used at the end of a workflow.
- **Note 6**: The PDB content must be valid, non-empty PDB text; malformed or empty structures will fail to visualize and can result in "None" or an empty viewer.

## Troubleshooting
- **Issue 1**: Output is "None". Ensure the pdb input is a non-empty dictionary of the form {id: pdb_content} and that the value is valid PDB text.
- **Issue 2**: Viewer shows a blank or empty page. Verify the structure content is valid PDB, check that the generated signed URL remains accessible, and confirm that the HTML output is rendered where external iframes are allowed.
- **Issue 3**: Uploads time out or fail. Check network connectivity and proxy or firewall settings, and confirm that backend storage and upload services are available before retrying.
- **Issue 4**: A different structure than expected is displayed with batches. Only the first available entry in the input PDB dictionary is visualized; reorder or filter upstream so the desired structure is first.
- **Issue 5**: Large PDB files are slow to appear. Large structures take longer to upload and render; allow more time or reduce structure size upstream if feasible.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/pdbvisualizationnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

