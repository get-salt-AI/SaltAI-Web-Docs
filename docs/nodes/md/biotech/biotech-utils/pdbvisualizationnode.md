# PDB Visualization

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates an embeddable HTML viewer for a protein structure. It uploads the provided PDB content to secure storage and returns a full-page HTML string that loads the structure in a Mol* (molstar.org) viewer. If a batch of PDBs is provided, only the first is visualized.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbvisualizationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to preview a protein structure during or after a workflow that produces or loads PDB data. Connect a PDB dictionary output (e.g., from a loader, combiner, or predictor) to quickly render an interactive 3D view in the app. The node returns an HTML string that can be routed to an Output/Display node.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Protein structure to visualize. Accepts a dictionary mapping an ID to PDB content. If multiple entries are provided (batch), only the first one is used.</td><td style="word-wrap: break-word;">{"proteinA": "ATOM      1  N   MET A   1      11.104  13.207  -2.222  1.00 20.00           N \n...\nEND"}</td></tr>
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
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">HTML string that embeds a full-page Mol* viewer iframe loading the uploaded structure.</td><td style="word-wrap: break-word;"><!DOCTYPE html><html><head><title>Full Screen Protein Viewer</title>...<iframe src="https://molstar.org/viewer/?structure-url=<encoded-signed-url>&structure-url-format=pdb&hide-controls=1&collapse-left-panel=1" allowfullscreen></iframe>...</html></td></tr>
</tbody>
</table>
</div>

## Important Notes
- Only the first structure in a batch is visualized.
- The node uploads the structure to secure storage and uses a signed URL in an external Mol* viewer (molstar.org).
- If extraction of a PDB from the input fails, the node returns the string "None".
- Viewer controls are minimized (controls hidden, left panel collapsed) for clean embedding.
- Execution priority is elevated so the preview is generated promptly when connected as an output node.
- The input PDB should be valid text content; malformed or empty structures will fail to visualize.

## Troubleshooting
- If output is "None": Ensure the pdb input is a non-empty dictionary {id: pdb_content} with valid PDB text.
- If the viewer shows a blank page: Verify the structure content is valid PDB and the signed URL remains accessible.
- If uploads time out or fail: Check network connectivity and that backend services for uploading are available; try again later.
- If you connected a batch and see a different structure than expected: Remember only the first available entry is used for visualization.
- Large PDB files may take longer to upload and render; allow additional time or reduce structure size if possible.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/pdbvisualizationnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

