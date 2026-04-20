# Equidock

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node docks one or more ligand protein structures into a single receptor structure using the EquiDock model behind a Salt biotech service. It sends the ligand and receptor PDB data, along with configuration options, to a remote EquiDock GPU service and waits for the docked result. The output is a PDB data object containing the ligand structure(s) transformed into the receptor’s coordinate frame, optionally post-processed to reduce steric clashes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/functional-prediction/equidocknode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need fast, learned protein–protein docking between a single receptor and one or more ligand structures, such as for protein–protein interaction modeling, binder design, or screening candidate binders against a known target. In a typical workflow, you first load or predict 3D structures (for example with a Load PDB node or an AlphaFold-based node), optionally clean or trim those structures, and then feed them into Equidock for docking. Place Equidock after any sequence-to-structure generation steps and before nodes that visualize complexes, compute interface metrics, or refine geometries.

The node supports multiple ligands but only a single receptor; if more than one receptor is provided, it will raise an error. The "dataset" setting selects which EquiDock training variant ("dips" or "db5") to use, affecting how well the model generalizes to different kinds of complexes. The "remove_clashes" option tells the backend to post-process ligand coordinates to reduce steric clashes at the interface, which is often helpful before running energy-based refinement or evaluation. This node integrates with Salt’s orchestration through hidden workflow context fields, so in normal use you only configure the visible scientific parameters (ligand_pdb, receptor_pdb, dataset, remove_clashes).

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
<tr><td style="word-wrap: break-word;">ligand_pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Ligand structure or structures to be docked, as a PDB data object. Internally this is typically a mapping from ligand identifiers (for example, chain labels or ligand names) to full PDB text strings. One or multiple ligands are supported; each is docked against the same receptor. Input PDB content must be syntactically valid (proper ATOM/HETATM records, coordinates, and termination records).</td><td style="word-wrap: break-word;">{"LIG_A": "HEADER LIGAND A\nATOM      1  N   ALA A   1      11.10  14.10  10.23  1.00 20.00           N\nATOM      2  CA  ALA A   1      12.35  14.80  10.90  1.00 20.00           C\nTER\nEND", "LIG_B": "HEADER LIGAND B\nATOM      1  N   GLY B   1      15.20  18.33   8.99  1.00 20.00           N\nATOM      2  CA  GLY B   1      16.41  19.01   9.60  1.00 20.00           C\nTER\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">receptor_pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Single receptor structure to dock against, as a PDB data object. The node requires exactly one receptor entry; multiple receptors are not supported and will cause an error. The receptor PDB should describe the binding partner or target protein in a consistent coordinate frame and contain valid ATOM/HETATM records and 3D coordinates.</td><td style="word-wrap: break-word;">{"RECEPTOR": "HEADER RECEPTOR\nATOM      1  N   MET R   1      25.10  30.45  12.33  1.00 18.00           N\nATOM      2  CA  MET R   1      26.30  31.10  13.05  1.00 18.00           C\nATOM      3  C   MET R   1      27.60  30.30  13.50  1.00 18.00           C\nTER\nEND"}</td></tr>
<tr><td style="word-wrap: break-word;">dataset</td><td>True</td><td style="word-wrap: break-word;">STRING_ENUM</td><td style="word-wrap: break-word;">Dataset variant that determines which EquiDock checkpoint is used on the backend. Valid values are "dips" and "db5". Choose the option whose training distribution best matches your docking scenario or benchmarking setup; different datasets can yield different docking behaviors and performance.</td><td style="word-wrap: break-word;">dips</td></tr>
<tr><td style="word-wrap: break-word;">remove_clashes</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, enables a post-processing step on the docked ligand structures to reduce steric clashes with the receptor. This may slightly adjust ligand positions relative to the raw EquiDock output but generally produces more physically plausible interfaces. If false, the raw docking poses from the model are returned without clash-resolution.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">bound_ligand.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Docked ligand structure or structures as a PDB data object. Each ligand is transformed into the receptor’s coordinate frame, with keys typically corresponding to the input ligand identifiers and PDB text containing updated atomic coordinates for the predicted bound pose. This output can be used by downstream nodes for visualization, interface analysis, scoring, or refinement.</td><td style="word-wrap: break-word;">{"LIG_A": "HEADER DOCKED LIGAND A\nATOM      1  N   ALA A   1      28.45  32.11  15.23  1.00 20.00           N\nATOM      2  CA  ALA A   1      29.70  32.80  15.90  1.00 20.00           C\nTER\nEND", "LIG_B": "HEADER DOCKED LIGAND B\nATOM      1  N   GLY B   1      30.90  29.10  14.40  1.00 20.00           N\nATOM      2  CA  GLY B   1      31.95  29.75  15.05  1.00 20.00           C\nTER\nEND"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Docking is performed by a remote GPU-accelerated EquiDock service with a fixed timeout (on the order of minutes). Very large receptor structures or many ligands in a single call will increase runtime and may approach timeout limits.
- **Limitations**: The node supports exactly one receptor structure. If receptor_pdb contains more than one entry, the node logs an error and raises a ValueError, and docking is not attempted.
- **Behavior**: The receptor structure is used as a static reference and is not modified or returned by this node; only ligand coordinates are transformed into the bound configuration. Keep the receptor from your upstream node if you need full complex visualization.
- **Behavior**: When remove_clashes is true, the backend may slightly adjust ligand positions compared with raw model predictions to reduce steric clashes, improving physical plausibility at the cost of small geometric changes at the interface.
- **Limitations**: Docking quality and robustness depend on how similar your complexes are to those in the chosen dataset ("dips" or "db5"). Unusual or out-of-distribution complexes may yield less reliable poses and can benefit from additional refinement or alternative docking approaches.
- **Performance**: Because this node depends on external Salt services and message-based communication, transient network or backend issues can cause intermittent failures or timeouts even when the scientific inputs are valid.

## Troubleshooting
- **Multiple receptors error**: If you see an error indicating that Equidock does not support multiple receptors, inspect the receptor_pdb input and ensure it contains exactly one key–value pair. Use upstream filtering or selection steps so only the intended receptor is passed in.
- **Timeouts or long runtimes**: If the node appears stuck or fails with a timeout, try reducing the number of ligands per run, trimming the receptor to the relevant domains, or rerunning later. Persistent timeouts may indicate backend load or issues; contact your Salt administrator if they continue.
- **Invalid PDB input**: When docking fails unexpectedly or the backend reports input problems, verify that ligand_pdb and receptor_pdb contain well-formed PDB text (correct ATOM/HETATM lines, coordinates, and termination records). Clean or regenerate structures with a PDB preparation tool or a dedicated structure-cleaning node.
- **Poor or clashing poses**: If resulting complexes have severe clashes or unrealistic interfaces, try enabling remove_clashes, switching the dataset setting between "dips" and "db5", or adding downstream refinement or minimization nodes. Trimming highly flexible or disordered regions before docking can also improve pose quality.
