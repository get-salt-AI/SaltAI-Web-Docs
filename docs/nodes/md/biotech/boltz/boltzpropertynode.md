# Boltz Property Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Constructs property specifications for Boltz YAML configurations. Currently supports defining an affinity property by specifying which chain is the binder (ligand). Validates inputs and outputs a property block ready to be combined into a full Boltz configuration.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpropertynode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to add property predictions to a Boltz run, such as computing binding affinity for a specified binder chain in a complex. Typically, create your sequences/templates/constraints, build the property with this node, then combine with other builders (e.g., list and YAML combiners) before running prediction or partial diffusion.

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
<tr><td style="word-wrap: break-word;">property_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select the type of property to predict. Currently only 'affinity' is supported.</td><td style="word-wrap: break-word;">affinity</td></tr>
<tr><td style="word-wrap: break-word;">binder_chain</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chain ID of the binder (ligand) within the complex. Must match the chain labeling used elsewhere in your configuration.</td><td style="word-wrap: break-word;">B</td></tr>
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
<tr><td style="word-wrap: break-word;">properties</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one property object. For affinity, the structure is {"affinity": {"binder": "<chain_id>"}}.</td><td style="word-wrap: break-word;">[{"affinity": {"binder": "B"}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Only 'affinity' is supported**: property_type must be 'affinity'; other values will raise an error.
- **Binder chain is required**: An empty or whitespace-only binder_chain will raise a validation error.
- **Chain ID consistency**: Ensure binder_chain matches the chain identifiers used in your sequence/template nodes.
- **Output is a list**: The node returns a list with a single property object; combine with other lists/configs as needed.
- **Intended for combination**: Use with Boltz List Combiner and Boltz YAML Combiner to assemble a full Boltz configuration before prediction.

## Troubleshooting
- **Error: 'Binder chain ID is required'**: Provide a non-empty chain ID (e.g., 'A', 'B', or a specific ligand chain).
- **Error: 'Unsupported property type'**: Set property_type to 'affinity'. Other values are not supported.
- **Predictions ignore affinity output**: Confirm you passed the properties output into the YAML/combiner nodes and into the predict node; also verify the binder_chain matches an actual chain in your complex.
- **Mismatched chain IDs**: If results look incorrect, re-check that the binder chain label used here exists and is consistent across all upstream nodes.
