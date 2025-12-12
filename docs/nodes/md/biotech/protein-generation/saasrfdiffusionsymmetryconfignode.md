# RF Diffusion Symmetry Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a symmetry configuration dictionary for RF Diffusion protein design. Validates the symmetry type and bundles additional symmetry-related options to influence how symmetric assemblies are generated.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionsymmetryconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to specify symmetry settings for RF Diffusion generation. Connect its output to the 'symmetry_config' input of the RF Diffusion node when designing symmetric proteins (e.g., cyclic Cn or dihedral Dn assemblies). Leave symmetry empty to disable symmetry-specific behavior.

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
<tr><td style="word-wrap: break-word;">symmetry</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Symmetry type to sample. Accepted values: 'cn' or 'dn' (e.g., 'c2', 'd5'), or one of 'tetrahedral', 'octahedral', 'icosahedral'. Leave empty to disable symmetry.</td><td style="word-wrap: break-word;">c3</td></tr>
<tr><td style="word-wrap: break-word;">recenter</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, repositions the assembly so its center aligns with the origin.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">radius</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Distance from the symmetry center used to place symmetric subunits. Must be >= 1.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">model_only_neighbors</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, model only neighboring subunits around the asymmetric unit instead of the full assembly.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">symmetric_self_cond</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, enforces symmetry during intermediate diffusion steps via self-conditioning.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">symmetry_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Validated symmetry configuration to pass into the RF Diffusion node.</td><td style="word-wrap: break-word;">{'symmetry': 'c3', 'recenter': True, 'radius': 12, 'model_only_neighbors': False, 'symmetric_self_cond': True}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Symmetry accepts only: empty, cn/dn (e.g., c2, d5), tetrahedral, octahedral, or icosahedral.
- Leaving symmetry empty disables symmetry constraints and returns symmetry as null in the config.
- This node does not run generation by itself; connect its output to the RF Diffusion node's 'symmetry_config' input.
- Radius must be a positive integer (>= 1).
- Options like 'model_only_neighbors' and 'symmetric_self_cond' change how symmetry is applied during sampling and may affect runtime and results.

## Troubleshooting
- Invalid symmetry value: Ensure the value matches 'cN' or 'dN' (e.g., 'c2', 'd5') or is one of 'tetrahedral', 'octahedral', 'icosahedral', or leave it empty.
- No effect observed: Confirm the symmetry_config output is connected to the RF Diffusion node and that your task/setup supports symmetric generation.
- Unexpected geometry: Adjust 'radius' and try toggling 'recenter' to better position the assembly.
- Partial or missing assembly: If only local context is modeled, check if 'model_only_neighbors' is set to true and switch it off for full assemblies.
