# Batch PDB

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Batch PDB lets you merge multiple PDB inputs (each a {pdb_id: pdb_string} dictionary) into one consolidated dictionary. It exposes a chain of pdb_1 to pdb_31 inputs that appear progressively as you connect them, then validates that all PDB IDs are unique before returning the merged batch. This is useful when you want to run downstream structure-processing or prediction workflows over multiple structures in one go.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/batchpdbnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you have several PDB inputs that you want to present as a single batched structure dictionary. Typical upstream nodes are LoadPDBNode (for individual PDB strings) or any node that outputs a PDB-typed dictionary like PDBCombinerNode, PDBChainExtractorNode, or PdbFixerNode. Place Batch PDB before downstream nodes that accept a PDB batch, such as PDBVisualizationNode, PDBToFastaNode, PDBChainExtractorNode, PdbFixerNode, or any structure-prediction or analysis step that iterates over PDB entries. A common pattern is: multiple LoadPDBNode feeding separate structures, then BatchPDBNode to combine them, followed by PDBToFastaNode or SaveToZipNode / SaveToBucketNode for exporting results. The node dynamically reveals pdb_2 through pdb_31 as you connect earlier inputs, making it convenient to scale from a few to many PDBs. Ensure each PDB dictionary you connect uses unique IDs (keys); if two inputs reuse the same ID, the node will raise an error instead of silently overwriting data.

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
<tr><td style="word-wrap: break-word;">pdb_1</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">First PDB input. Must be a dictionary mapping structure IDs to PDB content: {"pdb_id": "PDB text"}. This is mandatory; additional pdb_i inputs become available after this is connected.</td><td style="word-wrap: break-word;">{ "1abc_model1": "HEADER    EXTRACELLULAR PROTEIN\nATOM      1  N   MET A   1      11.104  13.207  10.342  1.00 18.26           N\nATOM      2  CA  MET A   1      12.560  13.384  10.532  1.00 17.44           C\nEND" }</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A single merged PDB dictionary that combines all connected inputs. The structure is {pdb_id: pdb_content}, where each key is a unique ID from the input dictionaries and each value is the raw PDB text. This output carries Salt metadata so that downstream nodes like SaveToZipNode or SaveToBucketNode can group and name files consistently.</td><td style="word-wrap: break-word;">{ "1abc_model1": "HEADER    EXTRACELLULAR PROTEIN\nATOM      1  N   MET A   1      11.104  13.207  10.342  1.00 18.26           N\nEND", "2xyz_complexA": "HEADER    SIGNALING PROTEIN\nATOM      1  N   GLY B   1      31.245  24.128  15.642  1.00 12.34           N\nEND", "rfdiffusion_design_001": "HEADER    DE NOVO DESIGNED PROTEIN\nATOM      1  N   GLY C   1      10.245  10.128   9.642  1.00 15.00           N\nEND" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Combining up to 31 PDB dictionaries is lightweight; the node primarily concatenates dictionaries and validates IDs, so runtime scales almost linearly with the total number of structures.
- **Limitations**: All PDB IDs across all inputs must be unique. If any ID appears more than once, even across different inputs, the node raises a ValueError and does not produce output.
- **Behavior**: Optional inputs pdb_2 through pdb_31 are hidden until the previous pdb_i is set; this is a UI behavior and does not affect runtime logic beyond which keyword arguments are present.
- **Behavior**: The node preserves each PDB string exactly as provided and does not attempt to validate PDB syntax or content; structural validation must be handled by downstream nodes if needed.

## Troubleshooting
- **Duplicate ID error**: If you see `Expected unique ids in PDBs but got <id> repeating twice.`, at least two inputs contain the same pdb_id key. Adjust upstream nodes (for example, change LoadPDBNode IDs or PDBCombinerNode settings) so every structure ID is unique.
- **Empty output or fewer structures than expected**: If the batched dictionary seems to contain fewer structures than you expect, verify that every connected pdb_i input actually carries a non-empty dict. Upstream nodes that encountered errors may output nothing, silently reducing the batch size.
- **Type mismatch issues**: If the node refuses to connect or run, check that each pdb_i input is typed as PDB (a {id: text} map), not a raw STRING. Use LoadPDBNode or other PDB-producing nodes to wrap raw PDB strings in the correct format before batching.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/batchpdbnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

