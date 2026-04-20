# RF Diffusion Symmetry Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node constructs a symmetry configuration object that controls how RFdiffusion applies global symmetry during protein generation. It validates and normalizes symmetry-related parameters such as symmetry type, recentering, assembly radius, neighbor-only modeling, and symmetric self-conditioning. The output is a structured config dictionary designed to plug directly into the symmetry_config input of the RF Diffusion node.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/protein-generation/saasrfdiffusionsymmetryconfignode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you want RFdiffusion to generate symmetric protein assemblies (for example cyclic, dihedral, or higher-order point group symmetries). It is typically placed immediately upstream of RF Diffusion (SaaSRFDiffusionNode) and connected to its symmetry_config input. A common workflow is: (1) Configure core generation parameters with RF Diffusion; (2) Optionally define sequence layout and masking via RF Diffusion Contigmap Config; (3) Optionally add scaffold-based conditioning via RF Diffusion Scaffold Guided Config; (4) Add RF Diffusion Symmetry Config to enforce the desired symmetry on the assembly. Typical use cases include designing cyclic oligomers (c2, c3, c4), dihedral assemblies (d2, d5), and tetrahedral/octahedral/icosahedral cages. As a best practice, start with moderate radius values and keep symmetric_self_cond enabled to encourage clean symmetry, and consider model_only_neighbors=true for large complexes to reduce computational load.

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
<tr><td style="word-wrap: break-word;">recenter</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, the generated symmetric complex is repositioned so that its geometric center aligns with the origin of the coordinate system. This is generally recommended for numerical stability and easier downstream analysis. Set to false only if you intentionally need to preserve an absolute coordinate frame.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">radius</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Specifies the distance from the symmetry center to the position where symmetric subunits are placed. Controls the approximate size of the symmetric ring or assembly. Must be at least 1; typical values for small cyclic oligomers fall in the 8–20 Å range, but you can increase this for more extended arrangements.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">model_only_neighbors</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, RFdiffusion models only the neighboring subunits of the asymmetric unit rather than the entire symmetric assembly. This keeps local interactions and interfaces consistent with the symmetry while reducing computational and memory costs.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">symmetric_self_cond</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, symmetry is enforced during intermediate steps of the diffusion process (self-conditioning), not just at the final step. This typically yields cleaner symmetric assemblies but may restrict diversity. Set to false if you want looser intermediate symmetry behavior.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">symmetry</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Type of symmetry to sample. Valid values are: empty string for no explicit symmetry; cyclic groups as 'cn' (for example 'c2', 'c3'); dihedral groups as 'dn' (for example 'd2', 'd5'); or one of 'tetrahedral', 'octahedral', 'icosahedral'. Leading and trailing whitespace is stripped and the value is converted to lowercase. Any other pattern will cause a validation error.</td><td style="word-wrap: break-word;">c3</td></tr>
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
<tr><td style="word-wrap: break-word;">symmetry_config</td><td style="word-wrap: break-word;">RFDIFFUSION_SYMMETRY_CONFIG</td><td style="word-wrap: break-word;">A validated configuration object (dictionary) containing symmetry parameters: recenter, radius, model_only_neighbors, symmetric_self_cond, and optionally a normalized symmetry field. This is designed specifically for the RFdiffusion backend and should be connected to the symmetry_config input of the RF Diffusion node. Downstream, RFdiffusion uses it to construct and maintain the specified symmetry during sampling.</td><td style="word-wrap: break-word;">{'recenter': True, 'radius': 12, 'model_only_neighbors': False, 'symmetric_self_cond': True, 'symmetry': 'c3'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: High-order symmetries (for example large dn groups or icosahedral) effectively increase system size and can be expensive; enabling model_only_neighbors can substantially reduce memory and compute.
- **Limitations**: The symmetry string must match either cN or dN with N as an integer, or exactly 'tetrahedral', 'octahedral', or 'icosahedral' (case-insensitive). Any deviation, including typos like 'c-3' or 'C02', will raise a ValueError.
- **Behavior**: If symmetry is left as an empty string it is converted to null internally, meaning no explicit symmetry type is requested even if other flags (like recenter) are set.
- **Behavior**: This node only prepares configuration data and does not itself perform protein generation; its output has an effect only when wired into the symmetry_config port of the RF Diffusion generation node.

## Troubleshooting
- **Invalid symmetry format**: If you see an error stating that symmetry was expected to be empty, cn/dn, or one of tetrahedral, octahedral, icosahedral, verify that your input string is either 'cN' or 'dN' with N as a positive integer (for example 'c4', 'd3') or one of 'tetrahedral', 'octahedral', 'icosahedral' without extra characters.
- **No visible symmetry in outputs**: When generated structures look asymmetric, first confirm that the symmetry_config output is connected to the RF Diffusion node input and that symmetry is not left empty. Also ensure there are no conflicting constraints in other config nodes that override the intended layout.
- **Unexpected complex size or spacing**: If subunits are too compact or too far apart, adjust the radius input upward or downward and re-run. Very small radii can cause steric clashes, while very large radii may disconnect interfaces.
- **Slow or memory-heavy runs**: For large symmetric assemblies where inference is slow or memory-intensive, consider setting model_only_neighbors=true and reducing design length via the contig configuration to limit the effective system size.
