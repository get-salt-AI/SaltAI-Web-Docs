# RF Diffusion Potentials Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a configuration object for guiding potentials used during RF Diffusion protein design. It parses a structured string describing one or more potentials, sets global guiding parameters (scale and decay), and defines oligomer contact behavior and optional substrate targeting. The output JSON is intended to be connected to the RF Diffusion node’s potentials_config input.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionpotentialsconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to bias RF Diffusion designs with energy-like guiding terms (e.g., radius of gyration, contact potentials, substrate interactions). Provide a semicolon-separated list of potential specifications and set global controls (guide_scale, guide_decay). Connect the resulting potentials_config to the RF Diffusion node to apply these constraints during sampling.

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
<tr><td style="word-wrap: break-word;">guiding_potentials</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Semicolon-separated potentials. Each potential is a comma-separated list of key:value pairs starting with type:<potential_type>. Supported types and defaults include, for example: monomer_ROG, binder_ROG, dimer_ROG, binder_ncontacts, interface_ncontacts, monomer_contacts, olig_contacts, substrate_contacts. Leave empty to disable potentials.</td><td style="word-wrap: break-word;">type:monomer_ROG,weight:1,min_dist:15;type:olig_contacts,weight_intra:1,weight_inter:0.1,r_0:8,d_0:2</td></tr>
<tr><td style="word-wrap: break-word;">guide_scale</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Global scale (strength) applied to guiding potentials.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">guide_decay</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schedule describing how potential strength changes over steps. Options: constant, linear, quadratic, cubic.</td><td style="word-wrap: break-word;">constant</td></tr>
<tr><td style="word-wrap: break-word;">olig_inter_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, apply inter-chain contact potential to all off-diagonal chain pairs.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">olig_intra_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, apply intra-chain contact potential within all chains (on-diagonal).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">olig_custom_contact</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Custom chain-pair contacts for oligomer potentials. Comma-separated pairs; '!' denotes repulsive, '&' denotes attractive.</td><td style="word-wrap: break-word;">A!B,B&C</td></tr>
<tr><td style="word-wrap: break-word;">substrate</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand residue name present in the input PDB (HETATM) used by substrate-focused potentials. Leave empty to disable.</td><td style="word-wrap: break-word;">LLK</td></tr>
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
<tr><td style="word-wrap: break-word;">potentials_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Configuration dictionary aggregating guiding_potentials list, global scaling/decay, oligomer contact flags/custom contacts, and substrate. Connect to the RF Diffusion node’s potentials_config.</td><td style="word-wrap: break-word;">{'guiding_potentials': ['type:monomer_ROG,weight:1,min_dist:15', 'type:olig_contacts,weight_intra:1,weight_inter:0.1,r_0:8,d_0:2'], 'guide_scale': 10, 'guide_decay': 'constant', 'olig_inter_all': False, 'olig_intra_all': False, 'olig_custom_contact': 'A!B,B&C', 'substrate': 'LLK'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- If guiding_potentials is left empty or parses to no valid entries, it is set to null (disables guiding potentials).
- Potentials must be semicolon-separated; each potential must include type:<name> and any parameters as key:value pairs separated by commas.
- substrate must match an actual ligand residue name in the input PDB; otherwise substrate-based potentials will have no effect.
- olig_custom_contact uses '!' for repulsion and '&' for attraction between chain IDs (e.g., A!B makes A-B repulsive).
- guide_scale controls overall strength; very high values can overly constrain sampling.
- This node only assembles configuration; it does not perform design. Connect its output to the RF Diffusion node’s potentials_config input.

## Troubleshooting
- Invalid potentials format: Ensure each segment looks like type:monomer_ROG,weight:1,min_dist:15 and segments are separated by semicolons.
- No effect from substrate potentials: Confirm the ligand residue name (e.g., LLK, HEM, ATP) exists in the input PDB as a HETATM and is spelled correctly.
- Unexpected oligomer contacts: Review olig_inter_all/olig_intra_all and any olig_custom_contact entries for unintended global contacts.
- Design collapses or fails to explore: Reduce guide_scale or switch guide_decay from constant to linear/quadratic/cubic.
- Too weak guidance: Increase guide_scale, or add/adjust specific potentials (e.g., interface_ncontacts with tuned r_0 and d_0).
