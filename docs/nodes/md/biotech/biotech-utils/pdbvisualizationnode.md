# PDB Visualization

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node accepts a protein structure as a PDB-format string, uploads it to a backend service, and builds a full HTML page that embeds the Mol* (molstar.org) viewer pointing at the uploaded structure. The output is a single HTML string ready to render for interactive 3D inspection of the protein. Upload handling, URL encoding, and viewer configuration are encapsulated inside the node.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbvisualizationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you want an immediate, interactive 3D visualization of a protein structure that you already have as a PDB string. It is best placed toward the end of a structural workflow, after nodes that load or transform PDB data such as LoadPDBNode, PDBCombinerNode, PdbFixerNode, PDBChainExtractorNode, or Alphafold-related nodes that output PDB files. The node posts the PDB text to a backend endpoint, which stores the structure and returns a structure URL. That URL is URL-encoded and injected into a Mol* viewer URL, all wrapped in a full-screen HTML template. Feed the resulting HTML into any component that can render full HTML documents (web dashboards, notebook outputs, custom viewers). For clean visualizations, do any fixing, filtering, or chain selection upstream and reserve this node purely for visualization.

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
<tr><td style="word-wrap: break-word;">pdb_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Complete protein structure content in standard PDB text format as a single string. It must contain valid PDB records (HEADER, TITLE, ATOM/HETATM lines, END, etc.) with line breaks preserved. Oversized or malformed PDB data can cause backend upload errors or timeouts.</td><td style="word-wrap: break-word;">HEADER    OXYGEN TRANSPORT                        07-JUL-21   1XYZ TITLE     HUMAN HEMOGLOBIN BETA CHAIN ATOM      1  N   VAL A   1      26.017  24.657  27.492  1.00 35.67           N ATOM      2  CA  VAL A   1      25.893  25.955  26.791  1.00 34.21           C ATOM      3  C   VAL A   1      24.458  26.413  26.451  1.00 33.10           C ... END </td></tr>
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
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A complete HTML document as a string embedding the Mol* viewer in a full-screen iframe, preconfigured to load the uploaded PDB structure via a structure URL from the backend. This is suitable for direct rendering in a browser, iframe, notebook output, or any UI that accepts full HTML pages.</td><td style="word-wrap: break-word;"><!DOCTYPE html> <html> <head>   <title>Full Screen Protein Viewer</title>   <style>     body, html {       margin: 0;       padding: 0;       width: 100%;       height: 100%;       overflow: hidden;     }     .viewer-container {       width: 100%;       height: 100vh;       border: none;     }     iframe {       width: 100%;       height: 100%;       border: none;     }   </style> </head> <body>   <div class="viewer-container">     <iframe src="https://molstar.org/viewer/?structure-url=https%3A%2F%2Fexample.org%2Fstructures%2Falpha_helix_model.pdb&structure-url-format=pdb&hide-controls=1&collapse-left-panel=1" allowfullscreen></iframe>   </div> </body> </html></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node sends the full PDB string via HTTP POST to a backend service and waits for a JSON response; large structures or slow networks can significantly increase latency and may reach the request timeout.
- **Limitations**: If the backend upload endpoint is unreachable or returns a non-200 HTTP status, the node raises an exception and no HTML is produced. There is no local-only visualization path.
- **Behavior**: The structure URL returned by the backend is URL-encoded and injected into a Mol* viewer URL with controls hidden and the left panel collapsed to maximize the 3D viewport.
- **Behavior**: The node does not validate, normalize, or repair PDB content. Invalid or corrupted PDB strings may upload successfully but fail to render or render incorrectly in the Mol* viewer.
- **Workflow**: Use upstream nodes like PdbFixerNode for structure repair or PDBChainExtractorNode for chain selection before visualization to improve reliability and reduce file size.

## Troubleshooting
- **Error message: `Upload protein to gcp failed`**: Indicates that the backend upload request failed or returned a non-200 status. Check backend availability, network connectivity, and whether the PDB string is reasonable in size and format. Test with a small, known-good PDB to distinguish content vs. connectivity issues.
- **Timeout or very slow response**: If the node appears to hang or times out, your PDB may be too large or the backend is under heavy load. Reduce the structure (for example, visualize one chain or domain) or retry when the service is less busy.
- **HTML renders but viewer is blank**: When the HTML page loads but Mol* shows nothing, inspect and URL-decode the `structure-url` parameter in the iframe `src` and verify it points to an accessible PDB resource. Ensure that storage endpoints and molstar.org are reachable and not blocked by firewalls or proxies.
- **Embedding issues in other UIs**: The output is a full HTML document, not a fragment. Some hosts that expect snippets may render it incorrectly. In those cases, strip the outer `<html>` and `<body>` tags and embed only the inner container and iframe, or load the document as a standalone page inside an iframe.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/pdbvisualizationnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

