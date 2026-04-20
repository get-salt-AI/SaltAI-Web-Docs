# RF Diffusion Potentials Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node builds the guiding potentials configuration used by RF Diffusion to steer protein generation toward desired structural and interaction properties. It parses a structured string that defines one or more potentials and sets global parameters such as strength, decay behavior, and oligomer contact patterns. The output is a standardized potentials_config object consumed directly by the RF Diffusion node.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionpotentialsconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need RF Diffusion to actively favor or disfavor certain physical behaviors, like compactness, intra-/inter-chain contacts, or interactions with a ligand. Typical scenarios include compact monomer design via radius-of-gyration potentials, oligomer interface tuning, interface or binder optimization, and substrate-focused design around a cofactor or small molecule. Place this node upstream of the main RF Diffusion node and connect its `potentials_config` output into the RF Diffusion node’s `potentials_config` input. It works well alongside RF Diffusion Symmetry Config (for symmetric oligomers), RF Diffusion Contigmap Config (for masking or constraining regions), and RF Diffusion Scaffold Guided Config (for fold-conditioned design). In practice, start with a single simple potential (such as `monomer_ROG`) and a moderate `guide_scale`, then gradually add more potentials or increase the scale while monitoring structural realism and interface quality.

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
<tr><td style="word-wrap: break-word;">guiding_potentials</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Semicolon-separated list of guiding potentials. Each potential is a comma-separated series of key:value pairs beginning with `type:<potential_type>`. Supported potential types and their default arguments include examples such as: `type:monomer_ROG,weight:1,min_dist:15`; `type:binder_ROG,weight:1,min_dist:15`; `type:dimer_ROG,weight:1,min_dist:15`; `type:binder_ncontacts,weight:1,r_0:8,d_0:4`; `type:interface_ncontacts,weight:1,r_0:8,d_0:6`; `type:monomer_contacts,weight:1,r_0:8,d_0:2,eps:1e-6`; `type:olig_contacts,weight_intra:1,weight_inter:1,r_0:8,d_0:2`; `type:substrate_contacts,weight:1,r_0:8,d_0:2,s:1,eps:1e-6,rep_r_0:5,rep_s:2,rep_r_min:1`. You can override defaults by specifying different argument values. Individual potentials are separated by `;`. The node splits, cleans, and filters this string; if no valid potentials remain, guiding_potentials is set to null in the configuration.</td><td style="word-wrap: break-word;">type:monomer_ROG,weight:1.2,min_dist:12;type:olig_contacts,weight_intra:0.8,weight_inter:0.2,r_0:8,d_0:2</td></tr>
<tr><td style="word-wrap: break-word;">guide_scale</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Global strength multiplier for all guiding potentials. A larger value increases the influence of potentials relative to the base diffusion process. Must be a non-negative integer. Practical values often lie between 1 and 20; excessively large values can overconstrain the design and may lead to unrealistic structures.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">guide_decay</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Schedule describing how the guiding potentials decay over diffusion steps. Allowed values are `constant`, `linear`, `quadratic`, and `cubic`. `constant` keeps potential strength the same at all steps, while `linear`, `quadratic`, and `cubic` progressively front-load the influence so that potentials matter more early in denoising and less toward the end.</td><td style="word-wrap: break-word;">quadratic</td></tr>
<tr><td style="word-wrap: break-word;">olig_inter_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls default inter-chain contact potentials for oligomers. If true, all off-diagonal chain pairs (different chains) are considered for oligomer contact potentials (for example, via `olig_contacts`) unless overridden by custom contact specifications. Use this to broadly encourage or penalize contacts between distinct chains.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">olig_intra_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls default intra-chain contact potentials. If true, all on-diagonal contacts (within a single chain) are subject to contact-based potentials, promoting compact or well-packed individual chains. Set this to false if you only want to enforce specific intra-chain behavior via particular potentials or regions.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">olig_custom_contact</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Custom specification of which chain pairs should have attractive or repulsive contact potentials. It is a comma-separated list of tokens in the form `Chain1OperatorChain2`, where `!` denotes repulsive contacts and `&` denotes attractive contacts. For example, `A!B` means repulsive contacts between chain A and B; `B&C` means attractive contacts between B and C. Leave this empty or whitespace-only to rely solely on `olig_inter_all` and `olig_intra_all`. The node trims the string and converts an empty result to null in the configuration.</td><td style="word-wrap: break-word;">A!B,B&C,C&D</td></tr>
<tr><td style="word-wrap: break-word;">substrate</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue name of a substrate or ligand present in the input PDB that substrate-based potentials will target (for example, when using `substrate_contacts`). This must match a ligand residue name (HETATM record) in the structure, such as a cofactor or inhibitor. The value is trimmed; if the final string is empty, it is stored as null and substrate-focused guidance will not be applied.</td><td style="word-wrap: break-word;">LLK</td></tr>
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
<tr><td style="word-wrap: break-word;">potentials_config</td><td style="word-wrap: break-word;">RFDIFFUSION_POTENTIALS_CONFIG</td><td style="word-wrap: break-word;">A structured configuration object containing all guiding potential settings for RF Diffusion. Internally it is a dict-like object with keys: `guiding_potentials` (list of cleaned potential definition strings or null), `guide_scale` (integer), `guide_decay` (string schedule), `olig_inter_all` (boolean), `olig_intra_all` (boolean), `olig_custom_contact` (normalized custom contact pattern string or null), and `substrate` (trimmed ligand residue name or null). Connect this directly to the `potentials_config` input of the RF Diffusion node to activate these guiding potentials during generation.</td><td style="word-wrap: break-word;">{ "guiding_potentials": ["type:monomer_ROG,weight:1.2,min_dist:12", "type:olig_contacts,weight_intra:0.8,weight_inter:0.2,r_0:8,d_0:2"], "guide_scale": 10, "guide_decay": "quadratic", "olig_inter_all": true, "olig_intra_all": false, "olig_custom_contact": "A!B,B&C,C&D", "substrate": "LLK" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Combining many guiding potentials with a high `guide_scale` can increase compute time on the RF Diffusion backend. For efficient runs, begin with a small number of potentials and moderate `guide_scale`, then adjust upward if the effect is too weak.
- **Limitations**: If `guiding_potentials` is empty or all entries reduce to empty or invalid after parsing, the node outputs `guiding_potentials` as null. This is valid and simply disables potential-based guidance rather than causing an error.
- **Behavior**: The node trims and normalizes its string inputs. `guiding_potentials` is split into a list and filtered; `olig_custom_contact` and `substrate` are stripped and converted to null when blank, allowing downstream logic to reliably detect whether they were set.
- **Behavior**: The node does not verify that chain identifiers in `olig_custom_contact` or the residue name in `substrate` actually exist in the PDB or contig setup used by RF Diffusion. It only formats these into configuration; you must ensure structural consistency yourself.
- **Limitations**: The node does not validate that all potential types or arguments are supported by the underlying RF Diffusion service. Typos or unsupported parameters may result in backend errors or be ignored, depending on backend implementation.

## Troubleshooting
- **Invalid guiding_potentials format**: If you encounter errors mentioning `guiding_potentials` or a specific potential index, check that each potential is defined as comma-separated key:value pairs and that separate potentials are separated by `;`. Remove trailing semicolons and avoid empty potential segments.
- **No visible effect from potentials**: If generated structures appear unchanged, verify that `guiding_potentials` is non-empty after trimming, that you used relevant potential types for your goal (for example, `monomer_ROG` for compactness or `interface_ncontacts` for interface design), and that `guide_scale` is sufficiently high (for example, 5–15 instead of 1).
- **Unexpected oligomer interfaces**: If oligomer interfaces form between the wrong chains or not at all, review `olig_inter_all`, `olig_intra_all`, and `olig_custom_contact`. Ensure that chain IDs in custom tokens (such as `A`, `B`, `C`) match those in your PDB or contig specification; mismatched IDs will cause those custom rules to be ineffective.
- **Weak or missing substrate interactions**: If designs do not interact with the intended ligand, confirm that `substrate` exactly matches the ligand residue name in your input PDB and that you have included a `substrate_contacts` potential in `guiding_potentials`. An empty or misspelled substrate name effectively disables substrate-based guidance.
