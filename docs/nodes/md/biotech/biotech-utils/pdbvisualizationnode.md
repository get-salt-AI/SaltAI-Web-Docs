# PDB Visualization

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates an HTML string that renders an interactive 3D protein structure viewer for a provided PDB. If a batch of PDBs is supplied, it automatically selects and visualizes the first one. The output is a full-page HTML viewer suitable for direct display in an app or browser.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbvisualizationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the end of a workflow when you want to preview a protein structure. Connect a PDB output (e.g., from Load PDB, PDB Combiner, or upstream processing nodes) to this node, then send the resulting HTML to your app's output or a display/output node to render the interactive viewer.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">PDB structure(s) to visualize. Accepts a dictionary mapping IDs to PDB content; if multiple are provided (batch), only the first entry is visualized.</td><td style="word-wrap: break-word;">{'protein_A': 'ATOM      1  N   MET A   1      11.104  13.207   8.678  1.00 44.46           N  \nATOM      2  CA  MET A   1      12.560  13.445   8.459  1.00 43.27           C  \nEND'}</td></tr>
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
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">An HTML document as a string that embeds an interactive protein viewer for the provided PDB.</td><td style="word-wrap: break-word;"><!DOCTYPE html><html><head><title>Full Screen Protein Viewer</title></head><body><div class="viewer-container"><iframe src="https://molstar.org/viewer/?structure-url=<signed_url>&structure-url-format=pdb&hide-controls=1&collapse-left-panel=1" allowfullscreen></iframe></div></body></html></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Only the first PDB in a batch is visualized. If you need to visualize a different entry, provide it as a single PDB or reorder your inputs.
- The viewer is loaded from an external visualization service. Network access is required for the embedded viewer to load correctly.
- If the PDB cannot be extracted or uploaded for preview, the output may be the string "None" instead of HTML.
- Best used as a terminal/preview step in a workflow to display the structure to end users.

## Troubleshooting
- Viewer is blank or not loading: Ensure you have network access and that external viewer domains are not blocked by your environment.
- Output is "None": Verify the input PDB is valid and non-empty; for batches, confirm the first item contains a proper PDB string.
- Wrong structure visualized: If using a combined/batch PDB input, remember this node visualizes the first available entry. Provide the intended PDB as a single input or adjust the batch order.
- Security/content policies: If your environment restricts external iframes, allowlist the viewer domain or render the HTML in a permitted context.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/pdbvisualizationnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

