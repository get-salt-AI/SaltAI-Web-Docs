# RF Diffusion Potentials Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node configures energetic guiding potentials for RF Diffusion protein design runs. It parses a semicolon-separated string of potential specifications, sets a global strength and decay schedule, and defines how inter-/intra-chain contacts and custom chain-pair contacts are treated. It also allows targeting a specific ligand (substrate) in the input structure for substrate-based potentials.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionpotentialsconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to steer RF Diffusion designs with additional energetic or geometric guidance instead of relying purely on the base diffusion model. Typical use cases include compact monomer design using radius-of-gyration (ROG) terms, enforcing or discouraging contacts within or between chains in oligomer design, shaping interfaces in binder or complex design, and focusing designs around a bound ligand using substrate contact potentials. In a typical workflow, you first define sequence layout and masking via SaaSRFDiffusionContigmapConfigNode, optionally add symmetry via SaaSRFDiffusionSymmetryConfigNode, adjust denoising via SaaSRFDiffusionDenoiserConfigNode, and optionally configure scaffold/fold conditioning with SaaSRFDiffusionScaffoldGuidedConfigNode. Then, configure this node to specify which potentials to apply, their global scale and decay, and any oligomer contact rules or ligand targeting, and connect its potentials_config output to the potentials_config input of SaaSRFDiffusionNode. Start with a small number of well-understood potentials and default weights, then iteratively adjust guide_scale or individual potential weights to refine behavior.

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
<tr><td style="word-wrap: break-word;">guiding_potentials</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Semicolon-separated list of guiding potential specifications. Each potential is a comma-separated set of key:value pairs and must include at least type:<potential_type>. Supported example templates (with default arguments) are: type:monomer_ROG,weight:1,min_dist:15; type:binder_ROG,weight:1,min_dist:15; type:dimer_ROG,weight:1,min_dist:15; type:binder_ncontacts,weight:1,r_0:8,d_0:4; type:interface_ncontacts,weight:1,r_0:8,d_0:6; type:monomer_contacts,weight:1,r_0:8,d_0:2,eps:1e-6; type:olig_contacts,weight_intra:1,weight_inter:1,r_0:8,d_0:2; type:substrate_contacts,weight:1,r_0:8,d_0:2,s:1,eps:1e-6,rep_r_0:5,rep_s:2,rep_r_min:1. Leave empty to disable guiding potentials. Whitespace is allowed around separators but the overall format must be correct; malformed entries may be dropped or cause downstream errors.</td><td style="word-wrap: break-word;">type:monomer_ROG,weight:1,min_dist:18;type:olig_contacts,weight_intra:0.8,weight_inter:0.2,r_0:8,d_0:2</td></tr>
<tr><td style="word-wrap: break-word;">guide_scale</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Global scale factor applied to all guiding potentials. Larger values increase the influence of potentials relative to the base diffusion model. Must be an integer; practical values are typically in the range 1–20 depending on desired strength.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">guide_decay</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schedule describing how the guide_scale decays over diffusion steps. Valid options are constant, linear, quadratic, and cubic. constant keeps the same strength throughout the trajectory, while the others progressively decrease the influence of potentials, with cubic emphasizing early steps most strongly.</td><td style="word-wrap: break-word;">linear</td></tr>
<tr><td style="word-wrap: break-word;">olig_inter_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to apply oligomer contact potentials to all off-diagonal entries in the chain contact matrix (all inter-chain pairs). Setting this to true encourages or enforces contacts between all chains globally; for large complexes or cases where only specific interfaces are desired, consider leaving this false and using olig_custom_contact instead.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">olig_intra_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to apply oligomer contact potentials to all diagonal entries in the chain contact matrix (within each chain). This can be used to encourage compactness or intramolecular contacts in every chain of a multimer. Leave false if you only want inter-chain or selectively specified contacts.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">olig_custom_contact</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of explicit chain-pair contact rules. Each entry has the form A!B for repulsive contacts (discouraging interaction) or A&B for attractive contacts (encouraging interaction). For example, A!B,B&C specifies that chains A and B are repulsive, while chains B and C are attractive. Leave empty to rely on olig_inter_all/olig_intra_all or potential defaults.</td><td style="word-wrap: break-word;">A!B,B&C</td></tr>
<tr><td style="word-wrap: break-word;">substrate</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand residue name from the input PDB that substrate-based potentials should target. This must correspond to a ligand defined with HETATM records in the structure; common examples include three-letter codes such as LLK, HEM, or ATP. The node trims whitespace and converts an empty string to null in the config, effectively disabling substrate-specific guidance if nothing is provided.</td><td style="word-wrap: break-word;">ATP</td></tr>
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
<tr><td style="word-wrap: break-word;">potentials_config</td><td style="word-wrap: break-word;">RFDIFFUSION_POTENTIALS_CONFIG</td><td style="word-wrap: break-word;">Structured configuration object encoding all guiding potential settings for RF Diffusion. It contains a list of parsed potential specification strings (or null if none), the global guide_scale, the guide_decay schedule, boolean flags for applying oligomer contacts to all intra-/inter-chain pairs, an optional olig_custom_contact pattern, and an optional substrate residue name. This object is intended to be connected directly to the potentials_config input of SaaSRFDiffusionNode, which forwards it into the potentials section of the RF Diffusion request.</td><td style="word-wrap: break-word;">{"guiding_potentials": ["type:monomer_ROG,weight:1,min_dist:18", "type:olig_contacts,weight_intra:0.8,weight_inter:0.2,r_0:8,d_0:2"], "guide_scale": 10, "guide_decay": "linear", "olig_inter_all": true, "olig_intra_all": false, "olig_custom_contact": "A!B,B&C", "substrate": "ATP"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Configuring many complex potentials, especially contact-based ones applied across multiple chains, can increase runtime and computational load in the RF Diffusion backend; start with a minimal set of potentials and scale up only if needed.
- **Limitations**: The node validates and normalizes list-like inputs but does not guarantee that each potential type or its arguments are supported by the RF Diffusion backend; typos or unsupported types may cause backend errors or silently reduced effect.
- **Behavior**: If guiding_potentials is empty or all entries become invalid after parsing, the node sets guiding_potentials to null in the output configuration, effectively disabling potential-based guidance even if other fields are set.
- **Behavior**: The substrate input is stripped of whitespace and turned into null if left blank, so substrate-centric potentials will not be applied unless you both specify substrate and include a suitable type:substrate_contacts potential in guiding_potentials.

## Troubleshooting
- **No noticeable effect from potentials**: Verify that guiding_potentials is non-empty and correctly formatted (no trailing semicolons, each entry contains type:), and consider increasing guide_scale or individual potential weights if the model output looks unchanged.
- **Backend error about unknown potential type or arguments**: Such errors usually indicate a typo or unsupported type or argument name in guiding_potentials. Cross-check each potential against the documented templates and remove or correct any problematic entries.
- **Ligand-focused design not working**: If protein–ligand interfaces are not forming as expected, confirm that the substrate residue name matches an actual HETATM ligand in your input PDB and that you have included a type:substrate_contacts term in guiding_potentials.
- **Unexpected oligomer contact patterns**: If your oligomer shows contacts between undesired chains or lacks expected interfaces, re-examine the combination of olig_inter_all, olig_intra_all, and olig_custom_contact. For fine-grained control, keep the global flags false and explicitly enumerate desired attractive or repulsive pairs in olig_custom_contact.
