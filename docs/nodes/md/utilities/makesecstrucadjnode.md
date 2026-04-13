# Make Secstruc Adj

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Generates secondary structure (_ss.pt) and block adjacency (_adj.pt) tensors from provided PDB structures. It prepares scaffold-conditioning artifacts required for fold-guided protein design workflows by validating the input PDBs and packaging paths to the generated tensors into a reusable reference.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/makesecstrucadjnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after you have assembled or loaded one or more PDB structures (for example via a PDB Loader or PDB Combiner node). Connect the resulting PDB object into this node to compute the scaffold secondary-structure and adjacency tensors and store them as .pt files. Feed the returned scaffold_pt_files output into downstream scaffold-guided configuration or protein generation nodes that expect these conditioning artifacts.

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
<tr><td style="word-wrap: break-word;">pdb_structures</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">One or more validated PDB structures that define the protein scaffold(s) from which secondary structure and block adjacency tensors will be derived. Typically comes from a PDB Loader, PDB Combiner, or an equivalent upstream node that outputs structured PDB data.</td><td style="word-wrap: break-word;"><pdb_object_from_pdb_loader></td></tr>
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
<tr><td style="word-wrap: break-word;">scaffold_pt_files</td><td style="word-wrap: break-word;">SCAFFOLD_PT_FILES</td><td style="word-wrap: break-word;">A reference bundle containing file paths or handles to the generated _ss.pt (secondary structure) and _adj.pt (block adjacency) tensors derived from the input PDB structures. This is intended to be consumed by scaffold-guided configuration and generation nodes.</td><td style="word-wrap: break-word;">{"ss_path": "/workspace/scaffolds/example_ss.pt", "adj_path": "/workspace/scaffolds/example_adj.pt"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input validity**: The node expects structurally valid PDB inputs; malformed or incompatible PDB files may cause processing to fail or yield unusable tensors.
- **Artifact format**: Outputs are stored as .pt tensor files, referenced via the scaffold_pt_files structure, not returned as raw tensors directly.
- **Workflow role**: This node is typically used once per scaffold setup and its output can be reused across multiple downstream design or generation runs that share the same scaffold.
- **Performance considerations**: Generating secondary structure and adjacency tensors can be compute-intensive for large or multiple PDBs; avoid unnecessary repeated runs by caching or reusing the scaffold_pt_files output.
- **Downstream compatibility**: Ensure that downstream nodes consuming scaffold_pt_files explicitly support the _ss.pt and _adj.pt formats produced here.

## Troubleshooting
- **No output or empty scaffold_pt_files**: Verify that the input PDB structures are non-empty, correctly formatted, and produced by a compatible upstream node.
- **Downstream node cannot read .pt files**: Confirm that you passed the scaffold_pt_files output directly into the expected scaffold-guided node input and that the environment has read access to the stored .pt paths.
- **Unexpected secondary structure or adjacency patterns**: Double-check that the input PDB corresponds to the intended scaffold (correct chains, residues, and alignment) and that no unintended modifications occurred upstream.
- **Node fails on specific PDBs**: Try simplifying or cleaning the PDB (removing non-standard residues, ligands, or alternate locations) and re-run; if problems persist, test with a known working PDB to isolate whether the issue is input-specific.
