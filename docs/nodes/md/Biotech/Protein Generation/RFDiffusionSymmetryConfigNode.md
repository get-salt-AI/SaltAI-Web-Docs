# RF Diffusion Symmetry Config

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds and validates a symmetry configuration for protein design runs. It normalizes and checks the requested symmetry (e.g., cyclic, dihedral, or polyhedral), and returns a JSON-ready configuration with additional options like recentring, placement radius, neighbor-only modeling, and symmetric self-conditioning.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Biotech/Protein Generation/RFDiffusionSymmetryConfigNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define symmetry behavior before running RF Diffusion protein generation. Connect its output to the RF Diffusion node’s symmetry configuration input to control symmetry type, placement radius, and whether symmetry is enforced during diffusion.

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
<tr><td style="word-wrap: break-word;">symmetry</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Type of symmetry to apply. Accepts 'cn' or 'dn' forms (e.g., 'c2', 'd5') or one of 'tetrahedral', 'octahedral', 'icosahedral'. Leave empty to apply no symmetry.</td><td style="word-wrap: break-word;">c3</td></tr>
<tr><td style="word-wrap: break-word;">recenter</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, repositions the complex so its geometric center is at the origin.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">radius</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Distance from the symmetry center where symmetric subunits are placed. Minimum value is 1.</td><td style="word-wrap: break-word;">20</td></tr>
<tr><td style="word-wrap: break-word;">model_only_neighbors</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, models only neighboring subunits of the asymmetric unit instead of the full symmetric assembly.</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">symmetry_config</td><td style="word-wrap: break-word;">JSON</td><td style="word-wrap: break-word;">Validated symmetry configuration to pass into the RF Diffusion node.</td><td style="word-wrap: break-word;">JSON object with keys: symmetry='c3', recenter=true, radius=20, model_only_neighbors=false, symmetric_self_cond=true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Symmetry formats**: Valid inputs are '', 'cN', 'dN' (e.g., c2, d5), or 'tetrahedral', 'octahedral', 'icosahedral'.
- **No symmetry**: An empty symmetry string is converted to null/none, meaning no symmetry will be applied.
- **Validation**: Invalid symmetry strings will cause the node to raise an error rather than produce a config.
- **Radius constraint**: The radius must be at least 1; choose values appropriate for your target assembly size.
- **Behavioral options**: 'model_only_neighbors' limits modeling to neighboring subunits; 'symmetric_self_cond' enforces symmetry during diffusion.

## Troubleshooting
- **Invalid symmetry error**: Ensure the symmetry string matches 'cN', 'dN' (N is an integer), or is one of 'tetrahedral', 'octahedral', 'icosahedral', or left empty.
- **Unexpected no-symmetry**: If symmetry is empty or whitespace, the node outputs a configuration with symmetry set to none; provide a valid string to enable symmetry.
- **Radius too small**: If radius is below 1, increase it to at least 1 to avoid validation issues.
- **Downstream rejection**: If the downstream node rejects the config, verify all booleans and the integer radius are set correctly and that symmetry is supported by the target workflow.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/Biotech/Protein Generation/RFDiffusionSymmetryConfigNode/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

