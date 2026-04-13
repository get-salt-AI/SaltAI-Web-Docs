# RF Diffusion Symmetry Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a symmetry configuration object for RF Diffusion runs. It validates the requested symmetry (cyclic, dihedral, or polyhedral) and bundles options like recentering, placement radius, neighbor-only modeling, and symmetric self-conditioning into a single structured config.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionsymmetryconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to impose symmetry during protein design with RF Diffusion. Configure the symmetry type (for example, c3, d5, tetrahedral) and related options, then connect the symmetry_config output to the symmetry_config input of the RF Diffusion node in your workflow.

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
<tr><td style="word-wrap: break-word;">symmetry</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Type of symmetry to apply. Accepts cn (for example, c2, c3), dn (for example, d5), or one of tetrahedral, octahedral, icosahedral. If left empty, symmetry is disabled.</td><td style="word-wrap: break-word;">c3</td></tr>
<tr><td style="word-wrap: break-word;">recenter</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to reposition the complex so that its geometric center is at the origin after applying symmetry.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">radius</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Distance from the symmetry center at which symmetric subunits are placed. Controls the overall size and spacing of the assembly.</td><td style="word-wrap: break-word;">12</td></tr>
<tr><td style="word-wrap: break-word;">model_only_neighbors</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, only neighboring subunits around the asymmetric unit are modeled instead of the full symmetric assembly.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">symmetric_self_cond</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, symmetry is enforced during intermediate diffusion steps (self-conditioning), not just in the final output.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">symmetry_config</td><td style="word-wrap: break-word;">RFDIFFUSION_SYMMETRY_CONFIG</td><td style="word-wrap: break-word;">Structured, validated symmetry configuration that controls RF Diffusion symmetric sampling behavior. Connect this to the symmetry_config input of the RF Diffusion node.</td><td style="word-wrap: break-word;">{"symmetry": "c3", "recenter": true, "radius": 12, "model_only_neighbors": false, "symmetric_self_cond": true}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Note 1**: The symmetry string must be in cn or dn format (for example, c2, c3, d5) or be one of tetrahedral, octahedral, icosahedral; any other value will cause an error.
- **Note 2**: Leaving the symmetry field empty or whitespace sets it to None internally, effectively disabling symmetry constraints.
- **Note 3**: This node only prepares configuration; it does not perform generation. You must connect symmetry_config into the RF Diffusion node for the settings to take effect.
- **Note 4**: The radius parameter directly influences assembly scale and subunit spacing; choose values consistent with the intended complex size.
- **Note 5**: Setting model_only_neighbors to true can reduce computational cost by avoiding modeling the full oligomer when only the local environment is needed.

## Troubleshooting
- **Invalid symmetry value**: If you encounter an error about symmetry, ensure it matches cn or dn (for example, c3, d5) or tetrahedral, octahedral, or icosahedral. For instance, change "c-3" to "c3".
- **No symmetry applied**: If designs appear asymmetric, check that the symmetry field is not blank. An empty value disables symmetry; set a value like "c2" to enable it.
- **Unexpected assembly size or placement**: If the complex is too compact or too spread out, adjust the radius input to better match the intended geometry.
- **Symmetry settings not affecting results**: Confirm the symmetry_config output is connected to the RF Diffusion node and ensure no downstream configuration overrides symmetry.
- **Runs are too slow or memory-heavy**: For large oligomers, try enabling model_only_neighbors to restrict modeling to neighboring subunits only.
