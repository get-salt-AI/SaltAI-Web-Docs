# RF Diffusion Potentials Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a configuration object for guiding potentials used by the RF Diffusion process. It parses a user-provided list of potential definitions, sets global scaling/decay behavior, and specifies oligomer contact behaviors and optional substrate targeting. Empty or missing fields are normalized to None where appropriate.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/RFDiffusionPotentialsConfigNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define and tune guiding potentials before running an RF Diffusion generation. Connect its output to the 'potentials_config' input of the RF Diffusion node alongside other configuration nodes (e.g., symmetry, contigmap, denoiser). Typical workflow: define potentials (e.g., contact or ROG constraints), select scale/decay, set oligomer contact options, optionally specify custom inter/intra chain contacts or a ligand residue to bias interactions.

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
<tr><td style="word-wrap: break-word;">guiding_potentials</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Semicolon-separated list of potential specifications. Each potential is a comma-separated key:value sequence beginning with type:potential_type followed by arguments. If left empty, no potentials are applied.</td><td style="word-wrap: break-word;">type:monomer_ROG,weight:1,min_dist:15;type:olig_contacts,weight_intra:1,weight_inter:0.1,r_0:8,d_0:2</td></tr>
<tr><td style="word-wrap: break-word;">guide_scale</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Global scale factor applied to all guiding potentials.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">guide_decay</td><td>True</td><td style="word-wrap: break-word;">constant\|linear\|quadratic\|cubic</td><td style="word-wrap: break-word;">Specifies how the guiding strength decays over inference steps.</td><td style="word-wrap: break-word;">constant</td></tr>
<tr><td style="word-wrap: break-word;">olig_inter_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, apply inter-chain contact potentials for all off-diagonal chain pairs.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">olig_intra_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, apply intra-chain contact potentials for all diagonal chain pairs.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">olig_custom_contact</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated custom contacts between chain pairs. Use '!' for repulsive and '&' for attractive interactions.</td><td style="word-wrap: break-word;">A!B,B&C</td></tr>
<tr><td style="word-wrap: break-word;">substrate</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand residue name in the input structure to target with substrate-based potentials. Must correspond to a ligand (HETATM) residue present in the input PDB.</td><td style="word-wrap: break-word;">HEM</td></tr>
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
<tr><td style="word-wrap: break-word;">potentials_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Configuration object capturing all guiding potential settings for consumption by the RF Diffusion node.</td><td style="word-wrap: break-word;">{'guiding_potentials': ['type:monomer_ROG,weight:1,min_dist:15', 'type:olig_contacts,weight_intra:1,weight_inter:0.1,r_0:8,d_0:2'], 'guide_scale': 10, 'guide_decay': 'constant', 'olig_inter_all': False, 'olig_intra_all': False, 'olig_custom_contact': 'A!B,B&C', 'substrate': 'HEM'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Guiding potentials format: semicolon-separated potentials; each potential is comma-separated key:value pairs starting with type:....
- Supported potential types and default args (as hints): monomer_ROG, binder_ROG, dimer_ROG, binder_ncontacts, interface_ncontacts, monomer_contacts, olig_contacts, substrate_contacts.
- If guiding_potentials is empty or all entries are blank, guiding_potentials is set to None in the output.
- Whitespace-only values for substrate are treated as None.
- olig_custom_contact accepts comma-separated pairs like A!B (repulsive) and B&C (attractive); empty value becomes None.
- guide_decay must be one of: constant, linear, quadratic, cubic.

## Troubleshooting
- Potentials not applied: Ensure guiding_potentials is non-empty and each potential is correctly formatted (start with type:<name> and use comma-separated key:value pairs).
- Invalid decay value: Use only constant, linear, quadratic, or cubic.
- No effect from substrate: Verify the specified residue name exists in the input structure as a ligand (HETATM); otherwise leave substrate blank.
- Unexpected global strength: Adjust guide_scale; too low yields weak influence, too high can overly constrain generation.
- Oligomer contacts missing: Set olig_inter_all and/or olig_intra_all to true or provide olig_custom_contact pairs explicitly.
- Parsing issues: Separate multiple potentials with semicolons and avoid trailing separators; within a potential, separate arguments with commas and use key:value format.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/protein-generation/RFDiffusionPotentialsConfigNode/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

