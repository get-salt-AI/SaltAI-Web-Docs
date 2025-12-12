# RF Diffusion Potentials Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a configuration object for guiding potentials used during RF Diffusion protein design. It parses a compact, user-provided specification of potentials, global scaling/decay parameters, oligomer contact rules, and optional substrate targeting, and outputs a validated JSON config. Empty or unset fields are normalized (e.g., empty strings become null) to ensure clean downstream consumption.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionpotentialsconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define and fine-tune guiding potentials before running RF Diffusion. Connect its output to the Potentials Config input of the RF Diffusion node. Typical workflow: define one or more potentials (semicolon-separated), set global guide scale and decay, specify oligomer contact rules as needed, and provide a substrate residue name if substrate-guided potentials are used.

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
<tr><td style="word-wrap: break-word;">guiding_potentials</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Semicolon-separated list of potentials. Each potential uses key:value pairs separated by commas. Example formats and defaults are provided below.</td><td style="word-wrap: break-word;">type:monomer_ROG,weight:1,min_dist:15;type:olig_contacts,weight_intra:1,weight_inter:0.1,r_0:8,d_0:2</td></tr>
<tr><td style="word-wrap: break-word;">guide_scale</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Global scale factor applied to guiding potentials.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">guide_decay</td><td>True</td><td style="word-wrap: break-word;">["constant", "linear", "quadratic", "cubic"]</td><td style="word-wrap: break-word;">Schedule describing how potential strength decays across diffusion steps.</td><td style="word-wrap: break-word;">constant</td></tr>
<tr><td style="word-wrap: break-word;">olig_inter_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, applies inter-chain contact potentials across all off-diagonal chain pairs.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">olig_intra_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, applies intra-chain contact potentials across all on-diagonal chain pairs.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">olig_custom_contact</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Custom oligomer contact map. Comma-separated chain-pair directives where '!' denotes repulsive and '&' denotes attractive contacts.</td><td style="word-wrap: break-word;">A!B,B&C</td></tr>
<tr><td style="word-wrap: break-word;">substrate</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand residue name present in the input PDB (HETATM) used by substrate-related potentials.</td><td style="word-wrap: break-word;">HEM</td></tr>
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
<tr><td style="word-wrap: break-word;">potentials_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Configuration dictionary compatible with the RF Diffusion node's potentials parameter.</td><td style="word-wrap: break-word;">{"guiding_potentials": ["type:monomer_ROG,weight:1,min_dist:15", "type:olig_contacts,weight_intra:1,weight_inter:0.1,r_0:8,d_0:2"], "guide_scale": 10, "guide_decay": "constant", "olig_inter_all": false, "olig_intra_all": false, "olig_custom_contact": "A!B,B&C", "substrate": "HEM"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Guiding potentials format: each potential is a comma-separated list of key:value pairs; multiple potentials are separated by semicolons.
- If no valid potentials are supplied, guiding_potentials is set to null, which disables explicit guiding potentials.
- olig_custom_contact accepts patterns like A!B (repulsive) and B&C (attractive), separated by commas.
- The substrate value must match a ligand residue name present in the PDB (HETATM). If left blank, it is set to null.
- Supported potential types and typical defaults include: monomer_ROG (weight:1,min_dist:15), binder_ROG (weight:1,min_dist:15), dimer_ROG (weight:1,min_dist:15), binder_ncontacts (weight:1,r_0:8,d_0:4), interface_ncontacts (weight:1,r_0:8,d_0:6), monomer_contacts (weight:1,r_0:8,d_0:2,eps:1e-6), olig_contacts (weight_intra:1,weight_inter:1,r_0:8,d_0:2), substrate_contacts (weight:1,r_0:8,d_0:2,s:1,eps:1e-6,rep_r_0:5,rep_s:2,rep_r_min:1).
- Connect this output to the 'potentials_config' input of the RF Diffusion node to apply guiding potentials during design.

## Troubleshooting
- Potential parsing errors: Ensure each potential uses key:value pairs separated by commas, and multiple potentials are separated by semicolons.
- Unknown decay type: guide_decay must be one of constant, linear, quadratic, cubic.
- Unexpected behavior from oligomer contacts: Verify chain IDs exist in your contig layout/PDB and that custom directives (A!B, B&C) are correctly formatted.
- Substrate not affecting results: Confirm the specified residue name exists as a ligand (HETATM) in the input PDB and matches exactly (e.g., ATP, HEM).
