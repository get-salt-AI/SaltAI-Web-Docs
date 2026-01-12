# RF Diffusion Potentials Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds the guiding potentials configuration for RF Diffusion runs. It parses human-readable potential specifications, global scaling/decay behavior, oligomer contact presets, and optional ligand/substrate targeting into a single structured config.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionpotentialsconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to steer RF Diffusion with physics-inspired guiding potentials (e.g., radius-of-gyration constraints, inter/intra-chain contacts, substrate interactions). Create and validate the potentials configuration here, then connect its output to the 'potentials_config' input of the RF Diffusion node.

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
<tr><td style="word-wrap: break-word;">guiding_potentials</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Semicolon-separated list of potentials. Each potential is 'type:potential_type,arg1:val1,arg2:val2,...'. Leave empty to disable. Supported examples include monomer_ROG, binder_ROG, dimer_ROG, binder_ncontacts, interface_ncontacts, monomer_contacts, olig_contacts, substrate_contacts.</td><td style="word-wrap: break-word;">type:monomer_ROG,weight:1,min_dist:15;type:olig_contacts,weight_intra:1,weight_inter:0.1,r_0:8,d_0:2</td></tr>
<tr><td style="word-wrap: break-word;">guide_scale</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Global scale factor for applying guiding potentials.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">guide_decay</td><td>True</td><td style="word-wrap: break-word;">['constant', 'linear', 'quadratic', 'cubic']</td><td style="word-wrap: break-word;">Time-schedule for how guiding potentials decay during the denoising process.</td><td style="word-wrap: break-word;">constant</td></tr>
<tr><td style="word-wrap: break-word;">olig_inter_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, applies inter-chain contact potential across all off-diagonal chain pairs.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">olig_intra_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, applies intra-chain contact potential on all diagonal chain pairs.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">olig_custom_contact</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated custom chain contact rules. Use '!' for repulsive and '&' for attractive interactions. Leave empty to skip.</td><td style="word-wrap: break-word;">A!B,B&C</td></tr>
<tr><td style="word-wrap: break-word;">substrate</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand residue name in the input PDB to target with substrate-based potentials. Must correspond to a ligand (HETATM). Leave empty if not used.</td><td style="word-wrap: break-word;">HEM</td></tr>
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
<tr><td style="word-wrap: break-word;">potentials_config</td><td style="word-wrap: break-word;">RFDIFFUSION_POTENTIALS_CONFIG</td><td style="word-wrap: break-word;">Structured potentials configuration to plug into RF Diffusion. Includes parsed potentials list, scaling/decay settings, oligomer contact flags, custom contacts, and optional substrate target.</td><td style="word-wrap: break-word;">{'guiding_potentials': ['type:monomer_ROG,weight:1,min_dist:15', 'type:olig_contacts,weight_intra:1,weight_inter:0.1,r_0:8,d_0:2'], 'guide_scale': 10, 'guide_decay': 'constant', 'olig_inter_all': False, 'olig_intra_all': False, 'olig_custom_contact': 'A!B,B&C', 'substrate': 'HEM'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Formatting of guiding_potentials**: Use semicolons to separate potentials, and commas to separate key:value pairs within each potential.
- **Supported potential examples**: monomer_ROG, binder_ROG, dimer_ROG, binder_ncontacts, interface_ncontacts, monomer_contacts, olig_contacts, substrate_contacts. Each has its own expected arguments and defaults.
- **Empty fields behavior**: Leaving guiding_potentials empty disables potentials (sets to null). Empty olig_custom_contact or substrate are treated as null.
- **Substrate requirement**: If using substrate-based potentials, the specified residue (e.g., HEM, ATP) must exist as a ligand (HETATM) in the input PDB provided to RF Diffusion.
- **Connection required**: This node only prepares configuration. Connect its output to the 'potentials_config' input of the RF Diffusion node to have any effect.
- **Oligomer contact flags**: olig_inter_all applies off-diagonal (inter-chain) contacts; olig_intra_all applies on-diagonal (intra-chain) contacts. Custom rules can refine or override.
- **Validation behavior**: The node trims and validates list-like inputs. If items fail parsing, they may be omitted, potentially resulting in an empty potentials list.

## Troubleshooting
- **Invalid guiding_potentials format**: Ensure each potential uses 'type:...,arg:val' pairs and potentials are separated by semicolons. Example: 'type:monomer_ROG,weight:1,min_dist:15'.
- **No effect during generation**: Verify the potentials_config output is connected to RF Diffusion, and that guiding_potentials is not empty if you expect guidance.
- **Substrate not recognized**: Confirm the ligand residue name exists in the input PDB as HETATM. If absent, either add the ligand or remove the substrate field.
- **Unexpected interactions in oligomers**: Review olig_inter_all/olig_intra_all settings and any olig_custom_contact rules for conflicts.
- **Overly strong guidance**: If designs collapse or fail, reduce guide_scale or use a gentler guide_decay (e.g., linear) to moderate the influence.
- **Parsing removed entries**: If some potentials disappear, check for stray spaces or missing key:value pairs; fix and retry.
