# Make Secstruc Adj

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node converts one or more input PDB protein structures into secondary-structure and block adjacency tensors required for scaffold-guided protein design workflows. It calls a managed CPU service that runs RFDiffusion's make_secstruc_adj.py script to produce paired *_ss.pt and *_adj.pt files for each input structure. The resulting files are returned inline as a scaffold_pt_files object that can be fed directly into downstream scaffold-conditioned configuration and generation nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/makesecstrucadjnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use Make Secstruc Adj after you have loaded or assembled one or more PDB structures and before any scaffold-guided protein generation or folding configuration nodes. A typical pipeline is: load PDBs (for example, via a PDB Loader or a cloud file loader node) → optionally merge or curate them with a PDB Combiner → Make Secstruc Adj → a scaffold-guided configuration node that drives RFDiffusion or similar protein design engines. The node expects a PDB object that maps structure names or identifiers to PDB-formatted text; for each entry it generates two .pt artifacts: a secondary-structure tensor (*_ss.pt) and a block adjacency matrix (*_adj.pt). The output scaffold_pt_files object is an inline, cloud-agnostic container for these artifacts and is designed to plug directly into downstream scaffold-guided configuration nodes. In practice, use this node whenever you need conditioning tensors for scaffold-based folding or design, and adjust the timeout parameter for large proteins or large batches.

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
<tr><td style="word-wrap: break-word;">pdbs</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Collection of PDB structure(s) to generate secondary-structure and adjacency .pt files from. This is a dict-like object where keys are scaffold or structure names and values are PDB-formatted strings. Must be a non-empty mapping; empty inputs, non-dict values, or invalid PDB content will cause the node to fail validation or the backend service to return no results.</td><td style="word-wrap: break-word;">{'scaffold_A': 'HEADER    SCAFFOLD A\nATOM      1  N   MET A   1      11.104  13.207   7.432  1.00 20.00           N\nATOM      2  CA  MET A   1      12.560  13.437   7.650  1.00 20.00           C\n...', 'scaffold_B': 'HEADER    SCAFFOLD B\nATOM      1  N   ALA A   1       8.231  10.512   5.024  1.00 18.50           N\nATOM      2  CA  ALA A   1       9.612  10.834   5.310  1.00 18.50           C\n...'}</td></tr>
<tr><td style="word-wrap: break-word;">timeout</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Maximum time in seconds to wait for the .pt file generation service to complete. Valid range is 60–1800 seconds; values below or above this range are rejected. Increase this for many scaffolds or very long proteins, and decrease it if you want faster failure on problematic inputs.</td><td style="word-wrap: break-word;">600</td></tr>
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
<tr><td style="word-wrap: break-word;">scaffold_pt_files</td><td style="word-wrap: break-word;">SCAFFOLD_PT_FILES</td><td style="word-wrap: break-word;">Inline scaffold .pt file payload containing secondary-structure and block adjacency tensors for each input PDB. The top-level object includes a type marker (set to "inline") and a pt_files mapping from scaffold name to its generated artifacts. Each scaffold entry typically includes *_ss.pt and *_adj.pt items encoded in a format expected by downstream scaffold-guided configuration and protein design nodes.</td><td style="word-wrap: break-word;">{'type': 'inline', 'pt_files': {'scaffold_A': {'scaffold_A_ss.pt': '<binary-encoded-ss-tensor>', 'scaffold_A_adj.pt': '<binary-encoded-adjacency-tensor>'}, 'scaffold_B': {'scaffold_B_ss.pt': '<binary-encoded-ss-tensor>', 'scaffold_B_adj.pt': '<binary-encoded-adjacency-tensor>'}}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Execution runs on a remote CPU service; large proteins or many scaffolds may take close to the timeout limit, so tune the timeout parameter (60–1800 seconds) based on batch size and complexity.
- **Limitations**: The node requires at least one valid protein PDB; empty dictionaries, non-dict inputs, or malformed PDB content can cause validation failures or result in no .pt files being produced.
- **Behavior**: Outputs are returned as inline data (type = "inline") instead of cloud paths, which simplifies cloud-agnostic workflows but can increase in-memory payload size for very large batches.
- **Behavior**: If the backend service completes but returns no scaffolds, the node raises an error instead of yielding an empty result so that invalid or incompatible PDB inputs are surfaced immediately.
- **Integration**: The output structure is tailored for downstream scaffold-guided configuration and RFDiffusion-based design nodes; other consumers should expect a dictionary keyed by scaffold name with *_ss.pt and *_adj.pt artifacts inside each entry.

## Troubleshooting
- **Common Error 1**: "ValueError: At least one PDB structure is required" – Ensure the pdbs input is a non-empty dict-like PDB object wired from a PDB Loader or PDB Combiner node, not left unconnected or set to null.
- **Common Error 2**: "No .pt files were generated. Check that PDB structures are valid." – One or more input PDBs are invalid or unsupported. Verify that each file contains standard protein ATOM records and no severe format issues, and regenerate or clean the inputs if needed.
- **Common Error 3**: Timeout or long-running execution – If generation regularly hits the timeout, increase the timeout value within the allowed 60–1800 second range, or reduce batch size by splitting your PDB set across multiple Make Secstruc Adj nodes.
- **Common Error 4**: Downstream node rejects scaffold_pt_files – Confirm that you connected the scaffold_pt_files output directly into a node that expects SCAFFOLD_PT_FILES, and that you did not accidentally convert or modify the output object in between.
