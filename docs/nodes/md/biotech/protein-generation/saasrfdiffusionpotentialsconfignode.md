# RF Diffusion Potentials Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds and validates the guiding potentials configuration for RF Diffusion runs. It parses a semicolon-separated list of potentials, sets global scaling and decay behavior, and configures oligomer contact rules and optional ligand-driven (substrate) contacts. Empty or omitted fields are normalized so downstream nodes receive a clean, structured config.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionpotentialsconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to steer RF Diffusion protein designs with energetic guiding terms such as radius of gyration, intra-/inter-chain contacts, and ligand interactions. Configure the potentials string, global scale and decay schedule, oligomer contact flags, and optional substrate name, then connect the output to the "potentials_config" input of the RF Diffusion node.

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
<tr><td style="word-wrap: break-word;">guiding_potentials</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Semicolon-separated list of guiding potentials. Each potential is a comma-separated set of key:value pairs starting with type:potential_type. If left empty or null, no guiding potentials are applied. Supports types such as monomer_ROG, binder_ROG, dimer_ROG, binder_ncontacts, interface_ncontacts, monomer_contacts, olig_contacts, and substrate_contacts.</td><td style="word-wrap: break-word;">type:monomer_ROG,weight:1,min_dist:15;type:olig_contacts,weight_intra:1,weight_inter:0.1,r_0:8,d_0:2</td></tr>
<tr><td style="word-wrap: break-word;">guide_scale</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Global scalar applied to all guiding potentials, controlling their overall strength during diffusion.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">guide_decay</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Schedule describing how guiding potential strength decays across diffusion timesteps. Allowed values: constant, linear, quadratic, cubic.</td><td style="word-wrap: break-word;">constant</td></tr>
<tr><td style="word-wrap: break-word;">olig_inter_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, enables inter-chain contact potentials for all off-diagonal chain pairs in an oligomeric design when oligomer contact potentials are used.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">olig_intra_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, enables intra-chain contact potentials for all chains (diagonal pairs) when oligomer contact potentials are used.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">olig_custom_contact</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Custom contact rules between specific chain pairs, overriding global oligomer contact defaults. Comma-separated entries; '!' denotes repulsive interactions and '&' denotes attractive interactions between chains.</td><td style="word-wrap: break-word;">A!B,B&C</td></tr>
<tr><td style="word-wrap: break-word;">substrate</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand residue name that appears as a HETATM record in the input PDB. When provided, enables substrate-based guiding potentials that bias the design around the specified ligand. If left empty, no substrate-based potentials are applied.</td><td style="word-wrap: break-word;">ATP</td></tr>
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
<tr><td style="word-wrap: break-word;">potentials_config</td><td style="word-wrap: break-word;">RFDIFFUSION_POTENTIALS_CONFIG</td><td style="word-wrap: break-word;">Structured configuration object encoding all guiding potential settings (parsed potentials list, global scale, decay schedule, oligomer contact flags, custom contact rules, and optional substrate name) for consumption by the RF Diffusion node.</td><td style="word-wrap: break-word;">{"guiding_potentials":["type:monomer_ROG,weight:1,min_dist:15","type:olig_contacts,weight_intra:1,weight_inter:0.1,r_0:8,d_0:2"],"guide_scale":10,"guide_decay":"constant","olig_inter_all":false,"olig_intra_all":false,"olig_custom_contact":"A!B,B&C","substrate":"ATP"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Guiding potentials string format**: Use semicolons to separate multiple potentials; within each potential, use commas to separate key:value pairs and always start with type:potential_type.
- **Supported potential types**: Typical options include monomer_ROG, binder_ROG, dimer_ROG, binder_ncontacts, interface_ncontacts, monomer_contacts, olig_contacts, and substrate_contacts; each has its own expected arguments and defaults.
- **Empty handling**: An empty or null guiding_potentials string results in no guiding potentials; empty olig_custom_contact and substrate values are treated as null and ignored.
- **Substrate requirement**: The substrate name must exactly match a ligand residue name present as a HETATM record in the input PDB for substrate-based potentials to have any effect.
- **Integration**: This node is purely configurational; it must be connected to the "potentials_config" input of the RF Diffusion node for the settings to influence the design run.

## Troubleshooting
- **Invalid potentials string**: If the configuration is rejected, check that each entry follows the pattern "type:NAME,key:value,..." and that individual potentials are separated by ";" characters.
- **Unknown potential type**: If a potential type is not recognized downstream, verify it is spelled correctly and is one of the supported types such as monomer_ROG, olig_contacts, or substrate_contacts.
- **Oligomer contact rules not applied**: When olig_custom_contact appears to have no effect, ensure chain IDs (for example, A, B, C) match those used in your contig or structure and that patterns like "A!B" or "B&C" are correctly formatted and comma-separated.
- **No effect from substrate-based potentials**: If substrate contacts seem inactive, confirm the ligand residue (for example, ATP, HEM) exists as a HETATM in the input PDB and that the residue name exactly matches the "substrate" field.
- **Overly strong or weak guidance**: If designs collapse or ignore guidance, adjust guide_scale and optionally switch guide_decay from constant to a decaying schedule (linear, quadratic, or cubic) to moderate the influence over time.
