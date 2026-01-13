# RF Diffusion Symmetry Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a symmetry configuration for RF Diffusion runs. It validates and formats the symmetry selection (e.g., cyclic, dihedral, or polyhedral), and bundles key options like recentring, placement radius, neighbor-only modeling, and symmetric self-conditioning into a single config object.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionsymmetryconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to enforce symmetry in protein design. Connect its output to the symmetry_config input of the RF Diffusion node. Choose the symmetry type (e.g., c3, d5, tetrahedral) and adjust options like recentring, radius, and whether to only model neighboring subunits.

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
<tr><td style="word-wrap: break-word;">symmetry</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Type of symmetry to sample. Accepted: cn (e.g., c2, c3), dn (e.g., d5), or tetrahedral, octahedral, icosahedral. Leave empty to disable symmetry.</td><td style="word-wrap: break-word;">c3</td></tr>
<tr><td style="word-wrap: break-word;">recenter</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, repositions the complex so its center aligns with the origin.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">radius</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Distance from the symmetry center to where symmetric subunits are placed.</td><td style="word-wrap: break-word;">12</td></tr>
<tr><td style="word-wrap: break-word;">model_only_neighbors</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, model only neighboring subunits of the asymmetric unit rather than the full assembly.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">symmetric_self_cond</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, enforces symmetry during intermediate diffusion steps (self-conditioning).</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">symmetry_config</td><td style="word-wrap: break-word;">RFDIFFUSION_SYMMETRY_CONFIG</td><td style="word-wrap: break-word;">Validated symmetry configuration to connect into the RF Diffusion node.</td><td style="word-wrap: break-word;">{'symmetry': 'c3', 'recenter': True, 'radius': 12, 'model_only_neighbors': False, 'symmetric_self_cond': True}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Symmetry must be either cn/dn format (e.g., c2, d5) or one of tetrahedral, octahedral, icosahedral. Any other value will raise an error.
- Leaving symmetry empty sets it to None, effectively disabling symmetry constraints.
- This node prepares configuration only; it does not perform generation. Connect its output to the symmetry_config input of the RF Diffusion node.
- Radius controls subunit placement relative to the symmetry center; ensure it is compatible with your intended assembly scale.
- model_only_neighbors reduces modeled scope to neighbors around the asymmetric unit; use when full assemblies are unnecessary.

## Troubleshooting
- Invalid symmetry value: Ensure the value matches cn (e.g., c3), dn (e.g., d5), or tetrahedral/octahedral/icosahedral. Example fix: change 'c-3' to 'c3'.
- No symmetry applied unexpectedly: If symmetry is blank or only whitespace, it is treated as None (disabled). Enter a valid value like 'c2' to enable.
- Unexpected assembly size/placement: Adjust the radius input to better match the intended size and spacing of subunits.
- Generated config not affecting results: Confirm the symmetry_config output is connected to the RF Diffusion node and no downstream node overrides symmetry settings.
