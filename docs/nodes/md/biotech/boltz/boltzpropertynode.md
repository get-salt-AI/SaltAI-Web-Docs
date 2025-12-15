# Boltz Property Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a Boltz-compatible property specification object. Currently supports configuring affinity prediction by specifying the binder (ligand) chain ID. The node validates inputs and produces a properties payload ready to be combined into a Boltz YAML configuration.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpropertynode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to request property predictions (e.g., affinity) for a modeled complex. Connect it alongside sequence/template/constraint builders, then merge outputs with a Boltz List/YAML Combiner before sending to a Boltz Predict or Partial Diffusion node. Provide the chain ID of the ligand/binder involved in the prediction.

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
<tr><td style="word-wrap: break-word;">property_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Type of property to predict. Currently only 'affinity' is supported.</td><td style="word-wrap: break-word;">affinity</td></tr>
<tr><td style="word-wrap: break-word;">binder_chain</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chain ID of the binder (ligand) in the complex for which the property will be predicted.</td><td style="word-wrap: break-word;">B</td></tr>
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
<tr><td style="word-wrap: break-word;">properties</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing a Boltz property object. For affinity, it specifies the binder chain to be used during prediction.</td><td style="word-wrap: break-word;">[{'affinity': {'binder': 'B'}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Only 'affinity' is supported**: Other property types are not accepted and will result in an error.
- **Binder chain is mandatory**: The binder_chain input must be a non-empty string corresponding to a valid chain in your complex.
- **Output is a list**: The node returns a list of property objects, enabling easy merging with other Boltz objects via a List or YAML Combiner.
- **Downstream compatibility**: Designed for integration into Boltz YAML configurations consumed by prediction nodes.

## Troubleshooting
- **Error: 'Binder chain ID is required'**: Provide a non-empty binder_chain value that matches your ligand chain ID (e.g., 'B').
- **Error: 'Unsupported property type'**: Ensure property_type is set to 'affinity'. No other values are supported.
- **Predictions ignore property settings**: Confirm the properties output is merged into the final YAML via a List/YAML Combiner before sending to the prediction node.
- **Chain mismatch issues**: Verify that the binder_chain matches the chain IDs defined in your sequence or template inputs.
