# RF Diffusion Potentials Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds and validates the guiding potentials configuration for RF Diffusion runs. It parses a semicolon-separated list of potentials, sets global scaling and decay behavior, and configures oligomer contact rules and optional ligand-driven (substrate) contacts. Empty or omitted fields are safely normalized so downstream nodes receive a clean, structured config.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionpotentialsconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to specify energetic guiding terms that shape designs (e.g., radius of gyration, contact potentials, substrate interactions). Connect its output to the 'potentials_config' input of the RF Diffusion node alongside your symmetry/contigmap/denoiser settings.

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
<tr><td style="word-wrap: break-word;">guiding_potentials</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Semicolon-separated list of potentials. Each potential is a comma-separated set of key:value pairs starting with type:potential_type. If left empty, no guiding potentials are applied.</td><td style="word-wrap: break-word;">type:monomer_ROG,weight:1,min_dist:15;type:olig_contacts,weight_intra:1,weight_inter:0.1,r_0:8,d_0:2</td></tr>
<tr><td style="word-wrap: break-word;">guide_scale</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Global scale factor applied to all guiding potentials.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">guide_decay</td><td>True</td><td style="word-wrap: break-word;">["constant", "linear", "quadratic", "cubic"]</td><td style="word-wrap: break-word;">Schedule describing how guiding strength decays across diffusion timesteps.</td><td style="word-wrap: break-word;">constant</td></tr>
<tr><td style="word-wrap: break-word;">olig_inter_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, enables inter-chain contact potentials for all off-diagonal chain pairs.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">olig_intra_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, enables intra-chain contact potentials for all chains (diagonal pairs).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">olig_custom_contact</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Custom contact rules between chain pairs. Comma-separated pairs; '!' denotes repulsive and '&' denotes attractive interactions.</td><td style="word-wrap: break-word;">A!B,B&C</td></tr>
<tr><td style="word-wrap: break-word;">substrate</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand residue name present as a HETATM in the input PDB. Enables substrate-based potentials when provided.</td><td style="word-wrap: break-word;">ATP</td></tr>
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
<tr><td style="word-wrap: break-word;">potentials_config</td><td style="word-wrap: break-word;">RFDIFFUSION_POTENTIALS_CONFIG</td><td style="word-wrap: break-word;">Structured configuration dict for guiding potentials to be consumed by the RF Diffusion node.</td><td style="word-wrap: break-word;">{'guiding_potentials': ['type:monomer_ROG,weight:1,min_dist:15', 'type:olig_contacts,weight_intra:1,weight_inter:0.1,r_0:8,d_0:2'], 'guide_scale': 10, 'guide_decay': 'constant', 'olig_inter_all': False, 'olig_intra_all': False, 'olig_custom_contact': 'A!B,B&C', 'substrate': 'ATP'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Guiding potentials format**: Use semicolons to separate potentials; within each potential, use commas to separate key:value pairs starting with type:...
- **Supported potential types**: monomer_ROG, binder_ROG, dimer_ROG, binder_ncontacts, interface_ncontacts, monomer_contacts, olig_contacts, substrate_contacts (each with specific arguments and defaults).
- **Empty handling**: Empty guiding_potentials becomes null (no potentials); empty olig_custom_contact and substrate become null.
- **Substrate requirement**: The substrate value must match a ligand residue name present in the input PDB (HETATM).
- **Connection**: Plug this node’s output into the 'potentials_config' input of the RF Diffusion node to take effect.

## Troubleshooting
- **Invalid potentials string**: If parsing fails, ensure each entry follows 'type:NAME,key:value,...' and entries are separated by ';'.
- **Unknown potential type**: Verify the type is one of the supported types; typos will lead to rejection downstream.
- **No effect from olig_custom_contact**: Check formatting (e.g., 'A!B' or 'B&C') and ensure chain IDs exist in your contig layout/inputs.
- **Substrate not applied**: Confirm the ligand residue (e.g., ATP, HEM) is present in the input PDB as HETATM and the name matches exactly.
