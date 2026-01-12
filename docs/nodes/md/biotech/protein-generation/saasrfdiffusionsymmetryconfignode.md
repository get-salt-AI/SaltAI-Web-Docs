# RF Diffusion Symmetry Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds and validates a symmetry configuration for RF Diffusion protein design. It normalizes the symmetry specification (e.g., c2, d5, tetrahedral) and bundles additional options like recentering, placement radius, neighbor-only modeling, and enforcing symmetric self-conditioning.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionsymmetryconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when designing symmetric protein assemblies. Connect its output to the symmetry_config input of the RF Diffusion node to control the target symmetry and related behaviors during sampling.

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
<tr><td style="word-wrap: break-word;">symmetry</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Symmetry type to sample. Accepts cyclic/dihedral forms (e.g., c2, d5) or named point groups (tetrahedral, octahedral, icosahedral). Leave empty to disable symmetry.</td><td style="word-wrap: break-word;">c3</td></tr>
<tr><td style="word-wrap: break-word;">recenter</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, repositions the assembly so its center is at the origin.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">radius</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Placement radius for symmetric subunits relative to the symmetry center. Must be >= 1.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">model_only_neighbors</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, model only neighboring subunits around the asymmetric unit instead of the full assembly.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">symmetric_self_cond</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, enforces symmetry during intermediate diffusion steps (self-conditioning).</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">symmetry_config</td><td style="word-wrap: break-word;">RFDIFFUSION_SYMMETRY_CONFIG</td><td style="word-wrap: break-word;">Validated symmetry configuration to drive symmetric assembly generation in RF Diffusion.</td><td style="word-wrap: break-word;">{"symmetry": "c3", "recenter": true, "radius": 12, "model_only_neighbors": false, "symmetric_self_cond": true}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Symmetry input is case-insensitive and is normalized internally (e.g., "C2" -> "c2").
- Allowed symmetry formats: cn or dn (e.g., c2, d6) and tetrahedral, octahedral, icosahedral. Empty string disables symmetry.
- If symmetry is empty, it is treated as None in the configuration.
- radius must be >= 1.
- Use symmetric_self_cond to help maintain symmetry throughout the diffusion process.
- model_only_neighbors can speed up sampling for large assemblies by restricting modeling to local neighbors.

## Troubleshooting
- Invalid symmetry format: Ensure the symmetry is '', a valid cn/dn (e.g., c2, d5), or one of tetrahedral/octahedral/icosahedral.
- Unexpected asymmetric outputs: Enable symmetric_self_cond and verify the symmetry string is correct.
- Geometry seems off-center: Set recenter to true and verify radius is appropriate for your assembly size.
