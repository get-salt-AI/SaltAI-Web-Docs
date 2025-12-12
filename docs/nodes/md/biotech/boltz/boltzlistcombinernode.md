# Boltz List Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines multiple Boltz objects into a single flat list. Accepts either single items or lists for each input and concatenates them into one combined list. Inputs are revealed progressively: each subsequent input becomes available only after the previous one is connected.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzlistcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to gather sequences, constraints, templates, and properties produced by other Boltz builder nodes into one list before feeding them into downstream nodes (e.g., Boltz YAML Combiner). Connect at least one input; you can provide up to 50 inputs, each being either a single object or a list of objects.

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
<tr><td style="word-wrap: break-word;">input_1</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">First Boltz object or list of Boltz objects (sequence, constraint, template, property, etc.). Must be connected to enable additional inputs.</td><td style="word-wrap: break-word;">{'protein': {'id': 'A', 'sequence': 'MGSS...'}}</td></tr>
<tr><td style="word-wrap: break-word;">input_2 ... input_50</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Additional Boltz object(s). Each input accepts a single object or a list of objects. Inputs appear only after the previous input is connected.</td><td style="word-wrap: break-word;">[{'ligand': {'id': 'L', 'smiles': 'CCO'}}, {'constraint': {'type': 'distance', 'chains': ['A', 'L']}}]</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_list</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A flat list containing all provided items. If any input was a list, its elements are appended; if an input was a single object, it is added as one element.</td><td style="word-wrap: break-word;">[{'protein': {'id': 'A', 'sequence': 'MGSS...'}}, {'ligand': {'id': 'L', 'smiles': 'CCO'}}, {'constraint': {'type': 'distance', 'chains': ['A', 'L']}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Inputs are progressive: input_2 is hidden until input_1 is connected, input_3 until input_2 is connected, and so on.
- Accepts up to 50 inputs (input_1 through input_50).
- The node flattens one level: lists provided on any input are extended into the combined list.
- At least one input must be connected; otherwise, the node raises an error.
- This node is typically used to assemble lists of sequences, constraints, templates, and properties before generating a final Boltz YAML.

## Troubleshooting
- Error: "At least one input is required" — Connect at least one input (input_1) with a valid object or list.
- Unexpected nesting in output — If you passed a list of lists, only one level of flattening occurs. Ensure each input is either a single item or a flat list of items.
- Downstream node rejects the combined list — Verify you combined compatible Boltz objects expected by the next node (e.g., provide only sequence objects when connecting to a sequences input).
- Cannot see additional inputs — Connect the current input (e.g., input_1) to reveal the next input (input_2).
