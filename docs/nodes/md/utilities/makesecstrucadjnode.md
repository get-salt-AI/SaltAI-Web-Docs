# Make Secstruc Adj

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node converts one or more protein PDB structures into paired secondary-structure and block-adjacency .pt tensors needed for scaffold-guided and fold-conditioning workflows with RFDiffusion-style models. It forwards the input PDBs to a dedicated CPU service that runs the make_secstruc_adj.py script, then validates the response and assembles a structured scaffold payload. The result is returned as SCAFFOLD_PT_FILES, ready for direct use in downstream scaffold-configuration nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/makesecstrucadjnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have 3D protein structures (PDB files) that you want to use as scaffolds or fold constraints in RFDiffusion-style design pipelines. Common scenarios include: (1) conditioning on a known functional motif, (2) constraining designs to an existing fold (e.g., an experimental structure or an AlphaFold prediction), or (3) batch-preparing many scaffolds for large design campaigns. A typical workflow is: load or combine structures with PDB Loader / PDB Combiner → Make Secstruc Adj → Scaffold Guided Config / design node. Place this node right after you have a finalized set of PDB structures and before any node that expects SCAFFOLD_PT_FILES. It is best used as the single, standardized conversion step from raw PDB coordinates to .pt tensors. For smoother runs, validate your PDBs (no missing chains you care about, no truncated residues at critical motifs) and adjust the timeout upward if you process very large structures or many files at once.

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
<tr><td style="word-wrap: break-word;">pdbs</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Mapping from structure names to PDB content. This must be a dictionary where each key is a unique structure identifier (string) and each value is the full PDB text (string) for that structure. The dict must not be empty; at least one valid PDB entry is required. This is typically the direct output of PDB Combiner or PDB Loader nodes, which already format PDBs into the expected mapping.</td><td style="word-wrap: break-word;">{'designed_helix_1': 'ATOM      1  N   MET A   1      11.104  13.207  10.236  1.00 20.00           N  ...', 'motif_complex': 'HEADER    IMMUNE SYSTEM                       15-JAN-22   7ABC               ...', 'alphafold_model': 'MODEL        1 ATOM      1  N   GLY A   1       2.345   1.234   0.456  1.00 20.00           N  ...'}</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the backend CPU service to generate the .pt files. Valid range is 60–1800 seconds. Increase this for large or many PDBs in a single batch; lower values cause faster failure if the service is overloaded or if something is wrong upstream.</td><td style="word-wrap: break-word;">600</td></tr>
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
<tr><td style="word-wrap: break-word;">scaffold_pt_files</td><td style="word-wrap: break-word;">SCAFFOLD_PT_FILES</td><td style="word-wrap: break-word;">Scaffold data bundle containing the generated .pt files for each input PDB. The value is a dict with a 'type' field (set to 'inline') and a 'pt_files' mapping. In 'pt_files', each key matches an input structure name and maps to a dict that references the corresponding secondary-structure (*.ss.pt) and adjacency (*.adj.pt) tensors. Downstream nodes that perform scaffold-guided design or fold conditioning can consume this output directly without additional formatting.</td><td style="word-wrap: break-word;">{'type': 'inline', 'pt_files': {'designed_helix_1': {'ss': 'gs://protein-scaffolds/run_042/designed_helix_1_ss.pt', 'adj': 'gs://protein-scaffolds/run_042/designed_helix_1_adj.pt'}, 'motif_complex': {'ss': 'gs://protein-scaffolds/run_042/motif_complex_ss.pt', 'adj': 'gs://protein-scaffolds/run_042/motif_complex_adj.pt'}}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Heavy computation is offloaded to a remote CPU service; runtime scales with both the number and the size of PDBs, so large batches may require a timeout near the upper limit (1800 seconds).
- **Limitations**: The node requires at least one valid PDB entry in a dictionary. Malformed PDBs or an empty mapping can lead to no .pt files being produced, in which case the node raises an error instead of returning partial results.
- **Behavior**: The node always wraps results in an 'inline' SCAFFOLD_PT_FILES structure with a 'pt_files' dict, which is optimized for workflows that pass scaffold data between nodes rather than relying on pre-existing cloud storage layout.
- **Behavior**: Input validation is strict: if `pdbs` is not a dict or is empty, the node fails immediately before invoking the backend service, helping to catch wiring or typing issues early in the pipeline.
- **Integration**: The node depends on a specific backend topic/service; if that service is down, misconfigured, or slow, you will see timeouts or a "No .pt files were generated" error rather than silent failures.

## Troubleshooting
- **Common Error 1**: "At least one PDB structure is required" – This means `pdbs` was empty or not a dictionary. Check that an upstream node like PDB Combiner is connected and actually producing a non-empty mapping.
- **Common Error 2**: "No .pt files were generated. Check that PDB structures are valid." – The backend returned no scaffolds. Inspect your PDB text for truncation or formatting issues, and try running a single small PDB to identify problematic inputs.
- **Common Error 3**: Long runtime followed by a timeout – The computation exceeded the configured `timeout`. Increase the timeout (up to 1800 seconds) or reduce the number/size of PDBs processed in one batch.
- **Common Error 4**: Downstream node rejects `scaffold_pt_files` – Ensure you connected the `scaffold_pt_files` output directly and did not modify its structure, and confirm the downstream node expects SCAFFOLD_PT_FILES rather than raw file paths or PDB text.
