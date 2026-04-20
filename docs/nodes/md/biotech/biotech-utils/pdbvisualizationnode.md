# PDB Visualization

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node takes a protein structure in PDB text format, uploads it to a backend service, and constructs a URL-safe link to the stored structure. It then embeds that URL into the Mol* web viewer, producing a full-page HTML document with an interactive 3D protein viewer. The output HTML can be rendered directly in a browser or any HTML-capable component for structural inspection.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbvisualizationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to visually inspect a protein structure represented as a PDB-formatted string, such as from AlphaFold predictions, PDB loaders, or structural processing pipelines. It is typically placed downstream of nodes that generate or load PDB data, such as LoadPDBNode, PDBCombinerNode, BatchPDBNode, or AlphaFold inference nodes, and upstream of nodes or systems that display or deliver HTML content (for example, report generators, dashboards, or web response handlers). Internally, the node sends the PDB string to an msa service endpoint that stores the file and returns a structure URL, which is URL-encoded and passed to the public Mol* viewer instance with controls minimized for a clean full-screen view. This pattern is useful for building interactive structural biology applications, including experiment summaries, protein design review tools, and internal visualization portals. For robust workflows, ensure network access to both the backend endpoint and the Mol* site, and validate your PDB input upstream if it is programmatically generated.

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
<tr><td style="word-wrap: break-word;">pdb_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Full protein structure in PDB format as a plain text string. This content is sent in a JSON body to a backend service that stores the structure and returns a URL to it. The string must be text-encoded PDB with standard records (for example, HEADER, TITLE, ATOM/HETATM lines) and should represent a valid structure; extremely large files or corrupted lines can cause upload or visualization failures.</td><td style="word-wrap: break-word;">HEADER    OXIDOREDUCTASE                         12-JAN-21   7XYZ TITLE     HUMAN BRD4 BROMODOMAIN IN COMPLEX WITH INHIBITOR ATOM      1  N   MET A   1      12.345  10.567   8.234  1.00 20.00           N ATOM      2  CA  MET A   1      13.456  11.234   8.901  1.00 19.50           C ATOM      3  C   MET A   1      14.321  10.876   9.765  1.00 18.70           C TER END </td></tr>
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
<tr><td style="word-wrap: break-word;">html</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A complete HTML document as a single string that embeds the Mol* viewer in full-screen mode, preloaded with the uploaded PDB structure. The HTML includes basic CSS that stretches an iframe to occupy the full viewport and sets the iframe source to a Mol* URL with a structure-url query parameter containing a URL-encoded link to the stored PDB file. Downstream components should treat this as full-page HTML and render it in a browser or any HTML-capable UI container.</td><td style="word-wrap: break-word;"><!DOCTYPE html> <html> <head>     <title>Full Screen Protein Viewer</title>     <style>         body, html {             margin: 0;             padding: 0;             width: 100%;             height: 100%;             overflow: hidden;         }         .viewer-container {             width: 100%;             height: 100vh;             border: none;         }         iframe {             width: 100%;             height: 100%;             border: none;         }     </style> </head> <body>     <div class="viewer-container">         <iframe src="https://molstar.org/viewer/?structure-url=https%3A%2F%2Fstorage.example.org%2Fproteins%2F7XYZ.pdb&structure-url-format=pdb&hide-controls=1&collapse-left-panel=1" allowfullscreen></iframe>     </div> </body> </html></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node makes a blocking HTTP POST request to upload the entire PDB string; large structures or slow networks increase runtime and may approach the configured timeout.
- **Limitations**: Successful visualization requires both the backend upload endpoint and the public Mol* viewer URL to be reachable; if either is unavailable, the generated HTML may render but show an empty or erroring viewer.
- **Behavior**: The structure URL returned by the backend is URL-encoded before being placed in the Mol* structure-url query parameter; unexpected or malformed URLs from the backend can still produce syntactically valid but nonfunctional HTML.
- **Behavior**: On any non-200 HTTP status or request exception, the node raises an error containing the message "Upload protein to gcp failed" along with details, and no HTML is output downstream.

## Troubleshooting
- **Error: Upload protein to gcp failed 400 - ...**: A 400-level error usually means the backend rejected the request (for example, invalid JSON body or malformed PDB text). Verify that pdb_string is non-empty, uses plain text encoding, and conforms to basic PDB formatting rules.
- **Error: Upload protein to gcp failed 500 - ...**: A 500-level error indicates a server-side issue. Check that the msa upload endpoint is configured correctly in your environment, inspect service logs if available, and retry with a smaller or simpler PDB file to rule out size-related problems.
- **Timeout or network-related exceptions**: If the node fails with a timeout or connection error, confirm that your environment can reach the backend upload service and that there are no firewall or DNS issues. For very large PDBs, consider reducing structure size (for example, stripping water or alternate conformations) before upload.
- **Viewer appears blank despite successful node run**: When the HTML renders but Mol* shows no structure, inspect the iframe src in the produced HTML and open it directly in a browser. If the structure-url within that query string is unreachable or returns an error, adjust backend configuration so it returns a valid, accessible URL.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/pdbvisualizationnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

