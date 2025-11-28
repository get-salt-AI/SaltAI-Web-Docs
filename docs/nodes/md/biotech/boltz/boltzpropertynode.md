# Boltz Property Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a properties object for Boltz YAML configurations. Currently supports defining an affinity property by specifying the binder (ligand) chain ID. Returns a list-wrapped property dictionary suitable for downstream combination and prediction.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpropertynode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to include property predictions (e.g., binding affinity) in a Boltz configuration. Typically, you will build sequences/templates/constraints, build one or more properties with this node, combine lists if needed, then merge into a final YAML config for prediction.

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
<tr><td style="word-wrap: break-word;">property_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select the property to predict. Currently only 'affinity' is supported.</td><td style="word-wrap: break-word;">affinity</td></tr>
<tr><td style="word-wrap: break-word;">binder_chain</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chain ID of the binder (ligand) for which to compute affinity.</td><td style="word-wrap: break-word;">B</td></tr>
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
<tr><td style="word-wrap: break-word;">properties</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one property dictionary for Boltz YAML (e.g., affinity settings). This is intended to be combined with other configuration parts before prediction.</td><td style="word-wrap: break-word;">[{'affinity': {'binder': 'B'}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Only 'affinity' is supported**: The property_type must be 'affinity'.
- **Binder chain is required**: A non-empty chain ID must be provided for the binder_chain input.
- **Output is list-wrapped**: The node returns a list containing the property dictionary, designed to be merged with other lists via list/YAML combiner nodes.
- **Chain IDs must match your setup**: Ensure the binder_chain corresponds to a valid chain defined in your sequence/template configuration.

## Troubleshooting
- **Error: 'Binder chain ID is required'**: Provide a non-empty string for binder_chain (e.g., 'A' or 'B').
- **Error: 'Unsupported property type'**: Only 'affinity' is allowed. Set property_type to 'affinity'.
- **No affinity results in downstream outputs**: Verify that the binder_chain matches a declared chain and that this properties output is correctly merged into the final configuration before prediction.
