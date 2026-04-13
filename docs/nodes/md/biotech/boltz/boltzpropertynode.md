# Boltz Property Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a Boltz-compatible property specification object for use in Boltz workflows. It currently supports affinity prediction by specifying the binder (ligand) chain ID and validates the provided inputs. The node outputs a properties payload ready to be merged into a full Boltz YAML configuration.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpropertynode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to request property predictions (such as affinity) for a modeled complex in a Boltz run. Configure the property type (currently only "affinity") and specify the binder chain ID corresponding to the ligand in your complex. Then combine this node’s output with sequence, template, and constraint objects using a Boltz List Combiner and Boltz YAML Combiner before sending the resulting configuration to Boltz Predict or Partial Diffusion nodes.

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
<tr><td style="word-wrap: break-word;">property_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Type of property to predict. Currently only "affinity" is supported; any other value is rejected.</td><td style="word-wrap: break-word;">affinity</td></tr>
<tr><td style="word-wrap: break-word;">binder_chain</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chain ID of the binder (ligand) in the complex for which the property (e.g., affinity) will be predicted. Must match a chain ID defined in your sequences/templates.</td><td style="word-wrap: break-word;">B</td></tr>
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
<tr><td style="word-wrap: break-word;">properties</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one Boltz property object configured according to the inputs. For affinity, this object specifies which binder chain to use during prediction.</td><td style="word-wrap: break-word;">[{"affinity": {"binder": "B"}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Only "affinity" is currently supported**: Setting property_type to anything else will cause validation to fail.
- **Binder chain is required and must be valid**: binder_chain must be a non-empty string that corresponds to a chain ID present in your complex definition (sequences/templates).
- **Output is a list of properties**: The node returns a list (even for a single property) so it can be merged directly with other Boltz objects via list or YAML combiner nodes.
- **Designed for downstream Boltz prediction**: This node’s output is intended to be included in Boltz YAML configurations consumed by prediction or partial diffusion nodes.
- **Position in workflow**: Typically used after or alongside sequence, template, and constraint builders, but before YAML combination and prediction.

## Troubleshooting
- **Error: "Binder chain ID is required"**: Ensure binder_chain is provided and is not empty; use a valid chain label such as "A" or "B" that exists in your complex.
- **Error: "Unsupported property type"**: Confirm that property_type is set exactly to "affinity"; no other property types are accepted.
- **Predicted results seem to ignore properties**: Verify that the properties output from this node is actually merged into your final YAML via Boltz List Combiner and Boltz YAML Combiner before running prediction.
- **Chain mismatch or failures in downstream nodes**: Double-check that binder_chain matches the chain IDs used in your sequence and template nodes; correct any inconsistencies and rebuild the YAML.
- **Multiple property objects needed**: Run this node multiple times and merge all resulting properties lists using a List Combiner node before creating the final YAML.
