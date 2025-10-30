# Prodigy

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Predicts binding affinity and related interface metrics for a protein complex. It analyzes the provided PDB structure, computes interaction features between specified chain groups, and returns a CSV table with scores such as predicted binding affinity, Kd, contact counts, NIS metrics, and temperature.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Biotech/Functional Prediction/ProdigyNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after you have generated or loaded a protein complex structure to estimate its binding affinity and interface characteristics. Provide the PDB(s) and specify which chains belong to which interacting molecules; the node will compute interface features and produce a consolidated score table. This is typically used after docking or complex prediction steps to prioritize candidates by predicted interaction strength.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">One or more protein complex structures to analyze. Each entry represents a PDB file content (text) keyed by a name. The node computes binding metrics for each provided structure.</td><td style="word-wrap: break-word;">{"complex_1.pdb": "ATOM ... END", "complex_2.pdb": "ATOM ... END"}</td></tr>
<tr><td style="word-wrap: break-word;">chains</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Defines which chains are considered as separate molecules (groups) for interaction analysis. Use semicolons to separate different molecules and commas to group multiple chains as a single molecule. If left empty, the whole complex is used.</td><td style="word-wrap: break-word;">A;B or A,B;C</td></tr>
<tr><td style="word-wrap: break-word;">temperature</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Temperature in Celsius used for Kd prediction.</td><td style="word-wrap: break-word;">25</td></tr>
<tr><td style="word-wrap: break-word;">distance_cutoff</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Atomic distance cutoff (in Å) to define interface contacts (ICs).</td><td style="word-wrap: break-word;">5.5</td></tr>
<tr><td style="word-wrap: break-word;">acc_threshold</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Accessibility threshold used for buried surface area (BSA) analysis.</td><td style="word-wrap: break-word;">0.05</td></tr>
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
<tr><td style="word-wrap: break-word;">score.csv</td><td style="word-wrap: break-word;">CSV</td><td style="word-wrap: break-word;">CSV table summarizing predicted binding scores per input structure, including binding_affinity, kd, contacts, contact_types, nis_a, nis_c, and temperature.</td><td style="word-wrap: break-word;">design,binding_affinity,kd,contacts,contact_types,nis_a,nis_c,temperature complex_1,-8.7,3.2e-6,56,hydrophobic:34;hydrogen_bonds:12,123.4,110.2,25</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Chain grouping syntax**: Use semicolons to separate different molecules and commas to group multiple chains within one molecule (e.g., A;B;C or A,B;C).
- **Empty chains input**: Leaving the chains field empty uses the entire complex without grouping.
- **Multiple inputs**: When multiple PDBs are provided, the node evaluates each and returns a combined CSV with one or more rows.
- **Parameter influence**: distance_cutoff and acc_threshold directly affect interface detection and BSA calculations; temperature affects Kd estimation.
- **Input expectations**: PDB must represent a protein complex with valid chain identifiers; ensure chain labels match those referenced in the chains field.

## Troubleshooting
- **No or unexpected interactions reported**: Verify that chains are specified correctly (e.g., A;B vs A,B;C). Incorrect grouping can cause missing or misleading interface metrics.
- **Chain label not found**: Ensure the chain IDs used in the chains field exactly match the labels present in the PDB file(s).
- **Unrealistic Kd values**: Check that temperature is appropriate and that the structure represents a biologically plausible interface.
- **Empty output or errors**: Make sure the PDB input is non-empty, properly formatted, and that at least two chain groups are defined or inferable.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/Biotech/Functional Prediction/ProdigyNode/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

