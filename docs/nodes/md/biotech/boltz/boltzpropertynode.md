# Boltz Property Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node constructs property specifications for Boltz YAML workflows, currently dedicated to binding affinity prediction. It wraps the chosen property type and binder chain identifier into a list of property objects that downstream Boltz execution nodes can interpret. The resulting structure is intended to be combined with other Boltz components such as sequences, templates, and constraints to form a complete Boltz specification.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpropertynode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want a Boltz-based workflow to compute a molecular property (currently binding affinity) in addition to or instead of structure generation. A typical setup defines the molecular system first using nodes such as BoltzSequenceNode, BoltzProteinSequenceNode, and BoltzLigandSequenceNode, which establish chain IDs (for example A, B, L). You then add BoltzPropertyNode to request an affinity calculation for a particular binder chain, usually the ligand chain. The node outputs a list of property objects that you usually merge with lists from BoltzConstraintNode, BoltzTemplateNode, and BoltzListCombinerNode before sending the aggregated specification to a downstream node that submits the Boltz job. Select this node whenever you need binding affinity scoring for a specific binder chain within your Boltz system.

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
<tr><td style="word-wrap: break-word;">property_type</td><td>True</td><td style="word-wrap: break-word;">STRING (choice: ['affinity'])</td><td style="word-wrap: break-word;">Specifies which property the Boltz workflow should calculate. The only supported option is "affinity", which instructs Boltz to evaluate binding affinity for the designated binder chain. Any other value will cause the node to raise an error.</td><td style="word-wrap: break-word;">affinity</td></tr>
<tr><td style="word-wrap: break-word;">binder_chain</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chain identifier of the binder (typically a ligand or peptide chain) whose binding affinity you want to predict. This must match a chain ID that has been defined upstream in your sequence or ligand nodes. Leading and trailing whitespace is ignored; an empty or whitespace-only value is not accepted.</td><td style="word-wrap: break-word;">L</td></tr>
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
<tr><td style="word-wrap: break-word;">properties</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one Boltz property object. For the "affinity" property type, its logical structure is equivalent to an element like {"affinity": {"binder": "<chain_id>"}} inside a list. This list-shaped output is designed to be merged with other Boltz lists such as sequences, templates, and constraints when assembling the final Boltz YAML specification.</td><td style="word-wrap: break-word;">[{'affinity': {'binder': 'L'}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs only simple validation and dictionary construction, adding negligible overhead compared to the actual affinity calculation carried out by the Boltz execution service.
- **Limitations**: The property_type input is currently constrained to "affinity". Any attempt to use another value results in an "Unsupported property type" error; additional properties would require explicit support in a future version of the node.
- **Behavior**: The binder_chain input is not cross-validated against existing sequences or ligands in the workflow. If you provide a chain ID that was never defined, the node will still succeed but downstream Boltz execution may fail or behave in unexpected ways.
- **Behavior**: The node always returns a list of property objects, even when only a single property is specified. This list-based structure is important for consistent combination with other Boltz object lists using nodes such as BoltzListCombinerNode.

## Troubleshooting
- **Common Error 1**: ValueError with message "Binder chain ID is required". This indicates that binder_chain was left empty or contained only whitespace. Resolve it by setting binder_chain to a valid chain ID, such as "L", that matches your binder or ligand chain.
- **Common Error 2**: ValueError with message "Unsupported property type: <value>". This occurs when property_type is set to a value other than "affinity". Change property_type back to "affinity"; other types are not supported in this node.
- **Common Issue 3**: A downstream Boltz job reports an unknown or missing binder chain. This usually means the binder_chain set here does not match any chain defined by your sequence or ligand nodes. Verify that the chain ID (including case) is consistent across all Boltz-related nodes.
- **Common Issue 4**: Affinity results are missing from the final output. Ensure that the properties output from this node is actually connected into the part of the workflow that builds and sends the complete Boltz specification, often by combining it with other lists through BoltzListCombinerNode.
