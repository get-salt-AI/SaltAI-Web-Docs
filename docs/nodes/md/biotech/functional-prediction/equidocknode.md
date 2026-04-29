# Equidock

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node performs rigid-body protein–protein docking using the EquiDock model. It takes one or more ligand protein structures and a single receptor protein structure in PDB format, applies a learned docking transform, and outputs the ligands in the receptor’s coordinate frame. You can select between supported EquiDock training datasets and optionally enable post-processing to remove steric clashes between docked partners.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/functional-prediction/equidocknode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to predict how a protein ligand binds to a protein receptor in 3D, particularly for protein–protein interaction modeling, interface analysis, or design workflows. A typical pipeline is: use a PDB loader node such as "Load PDB" (or any node producing PDB-type outputs) to load one or more ligand structures and exactly one receptor structure, feed these into EquidockNode, choose the dataset variant ("dips" for DIPS-like complexes or "db5" for Docking Benchmark 5-like cases), then send the docked ligand output into downstream nodes for scoring, visualization, or saving (for example, interface analysis, docking score computation, or "Save PDB"). In most workflows EquidockNode sits in the middle of the pipeline: upstream, structures are generated or retrieved (for example, from AlphaFold-based design nodes, RFDiffusion generation nodes, or database loaders), and downstream the docked complexes are evaluated for interface quality, energetics, or functional plausibility. When docking multiple ligands against a single receptor, provide all ligand PDBs together via the ligand_pdb input; the node will dock each ligand into the receptor frame and return the transformed ligand structures as a single PDB-typed output. For best results, ensure that receptor and ligand chains are properly cleaned (no severe clashes or missing residues at the binding interface) and that you pick the dataset variant that best matches your application domain. Enabling remove_clashes is helpful when you plan to export structures for downstream physics-based refinement or experimental use.

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
<tr><td style="word-wrap: break-word;">ligand_pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">One or more ligand protein structures to be docked into the receptor. This is a PDB-typed mapping from identifier to PDB string. Each value should be a valid PDB text representation of a protein chain or multichain ligand. Multiple ligands are supported and will all be docked against the same receptor. Structures should be in reasonable 3D conformations; there is no automatic remodeling or repair beyond optional clash removal.</td><td style="word-wrap: break-word;">{'ligand_1': 'ATOM      1  N   GLY A   1      11.546  13.207   2.561  1.00 20.00           N  \nATOM      2  CA  GLY A   1      12.321  12.015   2.150  1.00 20.00           C  \n...', 'ligand_2': 'ATOM      1  N   SER A   1      25.101  18.432  -3.221  1.00 18.00           N  \nATOM      2  CA  SER A   1      24.800  17.050  -2.800  1.00 18.00           C  \n...'}</td></tr>
<tr><td style="word-wrap: break-word;">receptor_pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Single receptor protein structure to dock ligands against. Must contain exactly one entry in the PDB-typed mapping; if more than one receptor is provided, the node raises a ValueError. The PDB string should describe the receptor in a consistent coordinate frame and with any required chains present. This serves as the fixed docking target.</td><td style="word-wrap: break-word;">{'receptor': 'ATOM      1  N   MET B   1       5.432  21.003   7.654  1.00 25.00           N  \nATOM      2  CA  MET B   1       6.120  20.100   8.600  1.00 25.00           C  \n...'}</td></tr>
<tr><td style="word-wrap: break-word;">dataset</td><td>True</td><td style="word-wrap: break-word;">STRING (enum: 'dips' \| 'db5')</td><td style="word-wrap: break-word;">Dataset variant that the EquiDock checkpoint was trained on. This controls which pre-trained model configuration is used. "dips" corresponds to a model trained on DIPS-like complexes, typically more diverse and larger interfaces; "db5" corresponds to a Docking Benchmark 5-like configuration. Choose the variant that best matches the type and difficulty of your docking problem. Default is "dips".</td><td style="word-wrap: break-word;">dips</td></tr>
<tr><td style="word-wrap: break-word;">remove_clashes</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to post-process docked ligands to resolve steric clashes between ligand and receptor atoms. When true, a clash-removal routine is applied after the EquiDock transform, slightly adjusting positions to reduce severe overlaps. This can improve downstream refinement stability but may slightly alter the original model geometry. Default is false.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">bound_ligand.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Docked ligand structure(s) in the receptor coordinate frame. This is a PDB-typed mapping from identifiers to PDB strings where each ligand has been transformed by the predicted EquiDock pose. Only ligands are returned; the receptor geometry is assumed to be unchanged and can be taken from the original receptor input. Downstream nodes can combine this with the receptor for complex visualization, compute interface metrics, or save final docked complexes.</td><td style="word-wrap: break-word;">{'ligand_1': 'ATOM      1  N   GLY A   1      15.210  24.331   5.772  1.00 20.00           N  \nATOM      2  CA  GLY A   1      16.002  23.150   5.300  1.00 20.00           C  \n...', 'ligand_2': 'ATOM      1  N   SER A   1      18.501  26.742   3.111  1.00 18.00           N  \nATOM      2  CA  SER A   1      19.020  25.420   2.730  1.00 18.00           C  \n...'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Docking is executed via a remote EquiDock GPU service with a timeout of about 300 seconds; large batches of ligands or high backend load can increase latency.
- **Limitations**: The node strictly requires exactly one receptor entry; providing multiple receptors will raise a ValueError, and the model does not perform multi-receptor or multi-body docking.
- **Limitations**: EquiDock performs rigid-body docking and does not model large conformational changes or loop remodeling; for flexible docking or detailed refinement, follow up with physics-based refinement tools.
- **Behavior**: When multiple ligands are provided they are all docked independently into the same receptor frame and returned together as a single PDB-typed output mapping, preserving their original identifiers.
- **Behavior**: Enabling remove_clashes may slightly perturb the docked geometry relative to the raw EquiDock output; this is generally beneficial for downstream energy calculations but may matter if you are strictly benchmarking raw model poses.

## Troubleshooting
- **Multiple receptor error**: If you see an error like "Equidock doesn't support multiple receptors! Please load a single receptor with Load PDB node.", check the receptor_pdb input and ensure it contains exactly one PDB entry. Merge or select a single receptor before calling this node.
- **Invalid PDB format**: If the docking run fails or returns no structures, verify that ligand_pdb and receptor_pdb contain well-formed PDB strings (proper ATOM/HETATM lines, coordinates, and chain identifiers). Try reloading from a trusted source or passing through a PDB cleaning node.
- **Timeouts or long runtimes**: When docking many ligands at once or under heavy service load, runs may approach the 300-second timeout. Reduce the number of ligands per call, split into smaller batches, or schedule runs during off-peak times.
- **Unexpected clashes after docking**: If large clashes remain even with remove_clashes enabled, inspect the receptor and ligand for overlapping starting positions or unrealistic structures. Consider pre-aligning, trimming extraneous chains, or performing additional refinement or relaxation downstream.
