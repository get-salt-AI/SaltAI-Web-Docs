# Boltz Property Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a Boltz-compatible properties object for downstream protein–ligand modeling. Currently supports generating an affinity property tied to a specified binder (ligand) chain ID.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpropertynode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to request an affinity prediction for a specific binder chain in a Boltz job. Typically, you build sequences (protein and ligand), add any constraints/templates, build properties with this node, combine all into a Boltz YAML or list, then run prediction.

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
<tr><td style="word-wrap: break-word;">property_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select the property to compute. Currently only 'affinity' is supported.</td><td style="word-wrap: break-word;">affinity</td></tr>
<tr><td style="word-wrap: break-word;">binder_chain</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chain ID of the binder (ligand) for which the property (e.g., affinity) should be computed.</td><td style="word-wrap: break-word;">A</td></tr>
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
<tr><td style="word-wrap: break-word;">properties</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing a single properties object suitable for Boltz YAML assembly. For affinity, it includes the binder chain mapping.</td><td style="word-wrap: break-word;">[{'affinity': {'binder': 'A'}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Only the 'affinity' property type is supported at this time.
- The binder_chain input is required and must be a non-empty chain identifier (for example, 'A').
- Output is returned as a single-element list of properties objects; use a combiner node to merge with sequences, constraints, and templates.

## Troubleshooting
- Error: 'Binder chain ID is required' — Provide a non-empty chain ID in the binder_chain input.
- Error: 'Unsupported property type' — Ensure property_type is set to 'affinity' as no other types are currently supported.
- If downstream nodes report missing properties, verify you passed this node’s output into your YAML or list combiner before prediction.
