# RF Diffusion Potentials Config

Builds a configuration object for guiding potentials used by the RF Diffusion node. It parses a semicolon-separated set of potential definitions, applies global scaling/decay settings, and optionally configures oligomer contact behaviors and a target substrate ligand. Empty or missing fields are normalized so downstream nodes receive a clean, consistent config.

## Usage

Use this node to define and bundle all potential-related controls for an RF Diffusion run. Typically, you will: (1) specify one or more guiding potentials, (2) set guide scale and decay behavior, (3) configure oligomer-wide contact preferences or custom contacts, and (4) optionally target a ligand residue as a substrate. Connect the resulting potentials_config output into the RF Diffusion node alongside other configs (e.g., symmetry, contigmap, denoiser).

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
<tr><td style="word-wrap: break-word;">guiding_potentials</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Semicolon-separated list of potentials. Each potential is defined as key:value pairs separated by commas. Required key: type. Other keys are potential-specific (e.g., weight, r_0, d_0). Supported examples include monomer_ROG, binder_ROG, dimer_ROG, binder_ncontacts, interface_ncontacts, monomer_contacts, olig_contacts, substrate_contacts. If left empty, no potentials are applied.</td><td style="word-wrap: break-word;">type:monomer_ROG,weight:1,min_dist:15;type:olig_contacts,weight_intra:1,weight_inter:0.1</td></tr>
<tr><td style="word-wrap: break-word;">guide_scale</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Global scale factor applied to guiding potentials.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">guide_decay</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Decay schedule for applying guiding potentials over steps. Allowed values: constant, linear, quadratic, cubic.</td><td style="word-wrap: break-word;">constant</td></tr>
<tr><td style="word-wrap: break-word;">olig_inter_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, applies contact potential to all off-diagonal (inter-chain) pairs in an oligomer.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">olig_intra_all</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, applies contact potential to all on-diagonal (intra-chain) regions within an oligomer.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">olig_custom_contact</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated custom contacts between chain pairs. Use '!' for repulsive and '&' for attractive interactions. Leave empty if not used.</td><td style="word-wrap: break-word;">A!B,B&C</td></tr>
<tr><td style="word-wrap: break-word;">substrate</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ligand residue name in the input PDB to target with substrate-based potentials. Must exist as a ligand (HETATM). Leave empty to disable.</td><td style="word-wrap: break-word;">LLK</td></tr>
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
<tr><td style="word-wrap: break-word;">potentials_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Configuration dictionary to pass into the RF Diffusion node that contains guiding potentials, scaling/decay, oligomer contact options, and optional substrate targeting.</td><td style="word-wrap: break-word;">{"guiding_potentials": ["type:monomer_ROG,weight:1,min_dist:15"], "guide_scale": 10, "guide_decay": "constant", "olig_inter_all": false, "olig_intra_all": false, "olig_custom_contact": "A!B", "substrate": "LLK"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Guiding potentials format**: Use semicolons to separate potentials and commas to separate key:value pairs within a potential. At minimum, include 'type:...' for each potential.
- **Normalization**: If guiding_potentials is empty, it is treated as None; if olig_custom_contact or substrate are empty strings, they are also treated as None.
- **Allowed decay values**: guide_decay must be one of constant, linear, quadratic, cubic.
- **Substrate requirement**: The substrate residue must exist in the provided PDB as a ligand (HETATM). Examples: LLK, HEM, ATP.
- **Oligomer contacts**: olig_inter_all and olig_intra_all enable global inter-chain or intra-chain contacts; use olig_custom_contact to specify fine-grained contacts by chain IDs.

## Troubleshooting
- **Invalid guiding_potentials syntax**: Ensure each potential follows 'type:...,arg1:val1,...' and potentials are separated by semicolons. Remove stray spaces or trailing separators.
- **Unsupported potential type or arg**: Verify 'type' and its arguments match supported potentials. Typos or unknown keys may cause downstream errors.
- **Decay value rejected**: Set guide_decay to one of: constant, linear, quadratic, cubic.
- **No such substrate in PDB**: Confirm the substrate residue name exists as a HETATM ligand in the input structure. If not, clear the substrate field or correct the residue code.
- **Unexpected oligomer behavior**: If contacts are too widespread or too sparse, review olig_inter_all/olig_intra_all flags and adjust or use olig_custom_contact to target specific chain pairs.
- **Over-strong guidance**: If designs collapse or diverge, try lowering guide_scale or using a softer guide_decay (e.g., linear) to reduce guidance intensity over steps.
