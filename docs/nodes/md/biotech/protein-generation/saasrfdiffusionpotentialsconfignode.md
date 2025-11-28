# RF Diffusion Potentials Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a configuration dictionary for guiding potentials used by the RF Diffusion protein design node. It parses user-provided potentials, decay behavior, oligomer contact options, and optional substrate targeting into a single JSON payload.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionpotentialsconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define and tune guiding potentials before running an RF Diffusion generation. Typically, you chain this node into an RF Diffusion workflow by feeding its output (potentials_config) into the main RF Diffusion node alongside other configs (e.g., symmetry, contigmap, denoiser).

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
<tr><td style="word-wrap: break-word;">guiding_potentials</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Semicolon-separated list of potentials. Each potential is a comma-separated series of key:value pairs. Use type:<potential_type> followed by its arguments.</td><td style="word-wrap: break-word;">type:monomer_ROG,weight:1,min_dist:15;type:olig_contacts,weight_intra:1,weight_inter:0.1</td></tr>
<tr><td style="word-wrap: break-word;">guide_scale</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Global scale factor applied to all guiding potentials.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">guide_decay</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">Decay schedule for applying guiding potentials across steps.</td><td style="word-wrap: break-word;">linear</td></tr>
<tr><td style="word-wrap: break-word;">olig_inter_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, apply contact potentials to all off-diagonal (inter-chain) positions.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">olig_intra_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, apply contact potentials to all on-diagonal (intra-chain) positions.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">olig_custom_contact</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated custom contacts between chain pairs. Use ! for repulsive, & for attractive.</td><td style="word-wrap: break-word;">A!B,B&C</td></tr>
<tr><td style="word-wrap: break-word;">substrate</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand residue name from the input structure targeted by substrate-based potentials. Must correspond to a ligand (HETATM).</td><td style="word-wrap: break-word;">HEM</td></tr>
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
<tr><td style="word-wrap: break-word;">potentials_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Configuration dictionary with all guiding potential settings, ready to be consumed by the RF Diffusion node.</td><td style="word-wrap: break-word;">{'guiding_potentials': ['type:monomer_ROG,weight:1,min_dist:15', 'type:olig_contacts,weight_intra:1,weight_inter:0.1'], 'guide_scale': 10, 'guide_decay': 'linear', 'olig_inter_all': False, 'olig_intra_all': False, 'olig_custom_contact': 'A!B,B&C', 'substrate': 'HEM'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The guiding_potentials string uses ';' to separate potentials and ',' to separate key:value pairs within each potential.
- Supported potential types and their default args include: monomer_ROG (weight,min_dist), binder_ROG, dimer_ROG, binder_ncontacts (r_0,d_0), interface_ncontacts (r_0,d_0), monomer_contacts (r_0,d_0,eps), olig_contacts (weight_intra,weight_inter,r_0,d_0), substrate_contacts (weight,r_0,d_0,s,eps,rep_r_0,rep_s,rep_r_min).
- If guiding_potentials is left empty or parses to an empty list, the value becomes null in the output (no guiding potentials).
- olig_custom_contact format: chain pairs separated by commas, using '!' for repulsive and '&' for attractive interactions (e.g., A!B,B&C).
- If substrate is empty or whitespace, it becomes null in the output; otherwise it must be a valid ligand residue present in the input structure.
- Allowed guide_decay values: constant, linear, quadratic, cubic.

## Troubleshooting
- Invalid guiding_potentials format: Ensure each potential is 'key:value' pairs separated by commas, and different potentials are separated by semicolons.
- Unexpected null guiding_potentials: Provide at least one well-formed potential; otherwise the node will output null for guiding_potentials.
- Substrate not applied: Verify the residue name matches a ligand (HETATM) in your input structure (e.g., LLK, HEM, ATP).
- Contacts not taking effect: If using olig_custom_contact, confirm chain IDs exist and use the correct symbols '!' (repulsive) and '&' (attractive).
- Over-aggressive guidance: Reduce guide_scale or choose a milder guide_decay (e.g., constant) to lessen the influence of potentials.
