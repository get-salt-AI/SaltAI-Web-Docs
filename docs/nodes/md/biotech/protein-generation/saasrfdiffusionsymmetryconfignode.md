# RF Diffusion Symmetry Config

Builds a symmetry configuration JSON for RF Diffusion-based protein design. It validates and encodes the requested global symmetry (cyclic, dihedral, or one of the Platonic solids) and related options like recentering, radius, and whether to model only neighbors or enforce symmetric self-conditioning. The output is intended to be connected into the RF Diffusion node as its symmetry_config input.

## Usage

Use this node when you want the RF Diffusion design to respect a specified point-group symmetry. Configure the symmetry and behavior here, then connect the output to the symmetry_config input of the RF Diffusion node. Leave symmetry empty to disable symmetry while retaining other defaults.

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
<tr><td style="word-wrap: break-word;">symmetry</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The target symmetry for the assembly. Accepts cyclic (cn, e.g., c2, c3), dihedral (dn, e.g., d5), or one of tetrahedral, octahedral, icosahedral. Empty string disables symmetry.</td><td style="word-wrap: break-word;">c3</td></tr>
<tr><td style="word-wrap: break-word;">recenter</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, repositions the assembly so its center aligns at the origin.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">radius</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Distance from the symmetry center to where symmetric subunits are placed. Must be >= 1.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">model_only_neighbors</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, model only neighboring subunits of the asymmetric unit instead of the full symmetric assembly.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">symmetric_self_cond</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, enforces symmetry during intermediate denoising steps (self-conditioning under symmetry).</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">symmetry_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Symmetry configuration JSON to feed into the RF Diffusion node.</td><td style="word-wrap: break-word;">{"symmetry":"c3","recenter":true,"radius":10,"model_only_neighbors":false,"symmetric_self_cond":true}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Accepted symmetry values**: '', cn (e.g., c2, c4), dn (e.g., d3, d5), tetrahedral, octahedral, icosahedral.
- **Empty symmetry**: An empty string is treated as no symmetry (disabled).
- **Validation**: Invalid symmetry strings raise an error; ensure the value matches the accepted formats.
- **Radius**: Must be >= 1; choose a radius consistent with your intended assembly scale.
- **Behavioral options**: model_only_neighbors limits modeling to neighbors; symmetric_self_cond enforces symmetry during diffusion; recenter aligns the complex to the origin.
- **Integration**: This node only prepares configuration; connect its output to the symmetry_config input of the RF Diffusion node.

## Troubleshooting
- **Invalid symmetry value error**: Use 'cn' or 'dn' with a number (e.g., c3, d5) or 'tetrahedral', 'octahedral', 'icosahedral'. Leave empty to disable symmetry.
- **No symmetry applied**: If symmetry is empty or whitespace, symmetry is disabled by design; provide a valid value to enable it.
- **Unexpected assembly size/placement**: Adjust radius to better match your desired subunit spacing.
- **Only neighbors modeled**: If the output lacks full assembly, set model_only_neighbors to false to model the entire symmetric assembly.
- **Downstream rejection**: Ensure the symmetry_config output is connected to the RF Diffusion node and that other RF Diffusion parameters are compatible with symmetry.
