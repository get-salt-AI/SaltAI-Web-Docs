# RF Diffusion Symmetry Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a validated symmetry configuration for RF Diffusion protein design. It lets you specify the symmetry type (cyclic, dihedral, or polyhedral), how structures are positioned around the symmetry center, and whether symmetry should be enforced during denoising.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionsymmetryconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when designing symmetric assemblies with RF Diffusion. Connect its output to the 'symmetry_config' input of the RF Diffusion node to control symmetry behavior during sampling. Typical workflow: define contigs and other configs, create a symmetry config here (e.g., c2 or d5), and feed it alongside other optional configs (contigmap/denoiser/potentials) into the RF Diffusion node.

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
<tr><td style="word-wrap: break-word;">symmetry</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Type of symmetry to sample. Allowed values: empty (no symmetry), cyclic (cn, e.g., c2), dihedral (dn, e.g., d5), or one of tetrahedral, octahedral, icosahedral.</td><td style="word-wrap: break-word;">c3</td></tr>
<tr><td style="word-wrap: break-word;">recenter</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, repositions the complex so its center aligns with the origin before applying symmetry.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">radius</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Distance from the symmetry center to where symmetric subunits are placed. Must be >= 1.</td><td style="word-wrap: break-word;">12</td></tr>
<tr><td style="word-wrap: break-word;">model_only_neighbors</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, model only neighboring subunits of the asymmetric unit rather than the full symmetric assembly.</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">symmetry_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Validated configuration dictionary to plug into the RF Diffusion node's symmetry_config input.</td><td style="word-wrap: break-word;">{"symmetry": "c3", "recenter": true, "radius": 12, "model_only_neighbors": false, "symmetric_self_cond": true}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Validation of symmetry**: The symmetry string must be empty, cn/dn (e.g., c2, d5), or one of tetrahedral/octahedral/icosahedral. Any other value raises an error.
- **No symmetry**: Leaving symmetry empty results in no explicit symmetry being enforced (it will be passed as null in the config).
- **Radius constraint**: radius must be an integer >= 1.
- **Intended use**: This node only prepares configuration. To take effect, connect its output to the RF Diffusion node.

## Troubleshooting
- **Invalid symmetry string error**: Ensure the value matches cn (e.g., c2), dn (e.g., d5), or tetrahedral/octahedral/icosahedral. Remove spaces and use lowercase (e.g., "c2").
- **Unexpected no symmetry applied**: If you leave symmetry empty, no symmetry is enforced. Provide a valid value like "c3" or "d2".
- **Radius out of range**: Set radius to a positive integer (>= 1).
- **No effect in results**: Verify the output of this node is connected to the 'symmetry_config' input of the RF Diffusion node and that the overall task supports symmetry.
