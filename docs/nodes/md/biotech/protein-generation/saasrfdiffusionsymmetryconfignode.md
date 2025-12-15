# RF Diffusion Symmetry Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a symmetry configuration for RFDiffusion-based protein generation. Validates and normalizes the symmetry type and bundles additional symmetry-related options into a single JSON object for downstream use. Empty symmetry is converted to no symmetry (None).

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionsymmetryconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to enforce symmetry during RF Diffusion protein generation. Connect its output to the symmetry_config input of the main RF Diffusion generation node within your workflow. Typical use: select a symmetry (e.g., c2, d5, tetrahedral), optionally adjust radius and toggles, then pass the resulting configuration to guide symmetric complex design.

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
<tr><td style="word-wrap: break-word;">symmetry</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Type of symmetry to sample. Accepts cyclic (cn, e.g., c2), dihedral (dn, e.g., d5), or one of tetrahedral, octahedral, icosahedral. Leave empty to disable symmetry.</td><td style="word-wrap: break-word;">c3</td></tr>
<tr><td style="word-wrap: break-word;">recenter</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, repositions the complex so its center aligns with the origin.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">radius</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Distance from the symmetry center to where symmetric subunits are placed. Minimum is 1.</td><td style="word-wrap: break-word;">12</td></tr>
<tr><td style="word-wrap: break-word;">model_only_neighbors</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, model only neighboring subunits of the asymmetric unit rather than the full assembly.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">symmetric_self_cond</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, enforces symmetry during intermediate steps of the diffusion process (self-conditioning).</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">symmetry_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">A JSON configuration object containing the processed symmetry settings to be merged into RF Diffusion inference configuration.</td><td style="word-wrap: break-word;">{"symmetry": "c3", "recenter": true, "radius": 12, "model_only_neighbors": false, "symmetric_self_cond": true}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Symmetry formats**: Only '', cn (e.g., c2), dn (e.g., d5), tetrahedral, octahedral, icosahedral are accepted.
- **Empty symmetry**: An empty string is converted to null, meaning no symmetry is applied.
- **Validation**: Invalid symmetry strings raise an error; ensure correct format and spelling.
- **Radius constraint**: Radius must be at least 1.
- **Defaults**: Defaults are recenter=true, radius=10, model_only_neighbors=false, symmetric_self_cond=true.

## Troubleshooting
- **Invalid symmetry error**: Ensure the value is '', cn/dn (e.g., c2, d5), or tetrahedral/octahedral/icosahedral. Use lowercase; spaces are not allowed.
- **No symmetry applied**: If symmetry is left empty, it is intentionally set to null (no symmetry). Provide a valid symmetry string to enable.
- **Unexpected geometry**: Adjust 'radius' to change placement distance of subunits; larger values spread subunits further from the center.
- **Performance concerns**: If modeling full assemblies is slow, set 'model_only_neighbors' to true to focus on local interactions.
- **Symmetry during diffusion**: If results appear asymmetrical mid-process, ensure 'symmetric_self_cond' is true to enforce symmetry throughout sampling.
