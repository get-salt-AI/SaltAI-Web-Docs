# Boltz Property Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node constructs a property specification object for use in Boltz YAML workflows. It currently supports only the "affinity" property type and requires specifying which chain acts as the binder (typically a ligand chain). The output object is validated for a non-empty binder chain ID and is intended to be merged into the `properties` section of the overall Boltz configuration.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpropertynode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you want Boltz to predict binding affinity for a complex that includes a ligand or other binder. First define your molecular entities using sequence-building nodes such as "BoltzProteinSequenceNode", "BoltzLigandSequenceNode", or "BoltzSequenceNode", giving each component a clear chain ID (for example, protein A and ligand L). Optionally add constraints via "BoltzConstraintNode" and templates via "BoltzTemplateNode". Then configure this node with `property_type` set to "affinity" and `binder_chain` set to the chain ID of the ligand (for example, "L"). Feed the output of this node, along with sequences, constraints, and templates, into "BoltzListCombinerNode" to create unified lists. Connect those lists to "BoltzYAMLCombinerNode" to produce `BOLTZ_YAML` and `BOLTZ_FILES`, which you finally send to "BoltzPredictNode" to run the calculation and obtain structures, confidence metrics, and affinity predictions. Ensure that the binder_chain you specify matches an actual ligand chain ID from your sequence definitions, otherwise the resulting configuration may not be valid for affinity prediction.

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
<tr><td style="word-wrap: break-word;">property_type</td><td>True</td><td style="word-wrap: break-word;">STRING (choice: 'affinity')</td><td style="word-wrap: break-word;">Specifies which property Boltz should compute. Currently this node supports only the value "affinity". Providing any other string causes the node to raise an error. This setting instructs Boltz to calculate binding affinity for the specified binder chain.</td><td style="word-wrap: break-word;">affinity</td></tr>
<tr><td style="word-wrap: break-word;">binder_chain</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The chain ID of the binder (typically a ligand chain) whose binding affinity is to be predicted. This must match one of the chain IDs declared in your sequence specification nodes (for example, a ligand chain defined by "BoltzLigandSequenceNode" or a ligand-form "BoltzSequenceNode"). The value cannot be empty or consist only of whitespace; if it is, the node raises a "Binder chain ID is required" error.</td><td style="word-wrap: break-word;">L</td></tr>
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
<tr><td style="word-wrap: break-word;">properties</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing a single Boltz property specification object. For an affinity setup, its logical structure is equivalent to a list with one dictionary entry where the key is "affinity" and the value includes the binder chain, such as [{"affinity": {"binder": "L"}}]. This list is meant to be combined with other Boltz objects via "BoltzListCombinerNode" and then connected into the `properties` input of "BoltzYAMLCombinerNode" so it becomes the properties section of the final Boltz YAML.</td><td style="word-wrap: break-word;">[{"affinity": {"binder": "L"}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Supported properties**: Only the "affinity" property type is supported. If you pass a different property_type, the node will raise an "Unsupported property type" error and not produce output.
- **Binder chain consistency**: The binder_chain must correspond to a chain ID actually defined in the sequences you specified. If it does not match a ligand or other binder chain, the downstream prediction may fail or produce invalid affinity outputs.
- **Requirement for affinity prediction**: BoltzPredictNode requires both at least one ligand sequence and at least one affinity property. Using this node without defining a ligand sequence will cause BoltzPredictNode to report that affinity prediction requires at least one ligand sequence.
- **List-wrapped output design**: The node always returns a list of property objects rather than a single bare dictionary, which allows easy concatenation with additional property specifications through "BoltzListCombinerNode" without additional formatting steps.

## Troubleshooting
- **Common Error 1: 'Binder chain ID is required'**: This means the binder_chain input was empty or only whitespace. Set binder_chain to a valid chain ID such as "L" that matches a ligand chain defined in your sequence builder nodes.
- **Common Error 2: 'Unsupported property type: <value>'**: You entered a property_type other than "affinity". Change property_type to "affinity" because this node does not implement any other property types at present.
- **Common Error 3: No affinity output from BoltzPredictNode**: If prediction completes but no affinity results appear, check that (1) a ligand sequence is present, (2) this node’s output is routed through "BoltzListCombinerNode" and into the `properties` input of "BoltzYAMLCombinerNode", and (3) binder_chain exactly matches the ligand chain ID.
- **Common Error 4: Downstream error 'Affinity prediction requires at least one ligand sequence'**: This indicates you successfully created an affinity property but did not define any ligand sequences. Add a ligand using "BoltzLigandSequenceNode" or a ligand-configured "BoltzSequenceNode" and ensure its chain ID equals the binder_chain used here.
