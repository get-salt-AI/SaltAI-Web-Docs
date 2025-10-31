# PDB Visualization

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates a full HTML page that visualizes a protein structure (PDB format) using the Mol* web viewer. It uploads the provided PDB string to a backend service, obtains a temporary structure URL, and embeds it in an iframe for interactive viewing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbvisualizationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a protein structure in PDB text format and want an immediately viewable, shareable HTML visualization. Typically used after a structure prediction or retrieval step to present results to end users or to embed the visualization in a report or UI.

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
<tr><td style="word-wrap: break-word;">pdb_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The complete protein structure in PDB text format. This content is uploaded to a backend service to generate a viewable structure URL.</td><td style="word-wrap: break-word;">ATOM      1  N   MET A   1      11.104  13.207   2.100  1.00 20.00           N  ...</td></tr>
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
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A standalone HTML document containing an iframe with the Mol* viewer preloaded to display the provided PDB structure.</td><td style="word-wrap: break-word;"><!DOCTYPE html><html><head><title>Full Screen Protein Viewer</title>...</head><body><div class="viewer-container"><iframe src="https://molstar.org/viewer/?structure-url=...&structure-url-format=pdb&hide-controls=1&collapse-left-panel=1" allowfullscreen></iframe></div></body></html></td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node depends on an external backend endpoint to upload the PDB text and return a structure URL; internet connectivity is required.
- If the upload request fails or the backend returns a non-200 response, the node raises an error with the reported status and message.
- The output is a full HTML page intended for embedding/display; ensure your consuming environment supports rendering iframes.
- The visualization uses the public Mol* viewer hosted at molstar.org; network or CSP restrictions may affect loading.
- Large PDB inputs can increase upload time and processing latency.

## Troubleshooting
- Upload failed with non-200 response: Verify the backend service is reachable and credentials/configuration (ENDPOINTS) are correct; check the error message returned by the service.
- Viewer does not load or stays blank: Ensure internet access to molstar.org and that your environment allows iframes; check browser console for CSP or network errors.
- Invalid or malformed PDB: Validate the PDB string format; some services reject nonstandard or corrupted inputs.
- Slow or timed-out requests: Reduce PDB size if possible, retry on a more stable connection, or adjust timeout/settings if available in your environment.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/pdbvisualizationnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

