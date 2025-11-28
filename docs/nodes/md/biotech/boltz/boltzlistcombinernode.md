# Boltz List Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines multiple Boltz objects into a single ordered list. It accepts up to 50 inputs, each of which can be a single Boltz object or a list of Boltz objects, and flattens one level into a consolidated list. Useful for gathering sequences, constraints, templates, and properties before generating a final Boltz YAML.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzlistcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to collect and merge items produced by Boltz builder nodes (e.g., sequences, constraints, templates, properties) into one list. Connect the outputs from those builder nodes to input_1, input_2, etc.; new inputs appear progressively after the previous one is connected. Feed the resulting combined list into the Boltz YAML Combiner node to assemble a full configuration.

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
<tr><td style="word-wrap: break-word;">input_1</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">First Boltz object or list of objects to include. Accepts sequence, constraint, template, or property items.</td><td style="word-wrap: break-word;">[{'protein': {'id': ['A'], 'sequence': 'MA...'}}]</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Second Boltz object or list of objects to include. This input becomes available after input_1 is connected.</td><td style="word-wrap: break-word;">[{'ligand': {'id': ['L'], 'smiles': 'CCO'}}]</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Third Boltz object or list of objects to include. Becomes available after input_2 is connected.</td><td style="word-wrap: break-word;">[{'constraint': {'type': 'distance', 'chains': ['A', 'L'], 'threshold': 5.0}}]</td></tr>
<tr><td style="word-wrap: break-word;">input_4 ... input_50</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Additional Boltz objects or lists of objects. Each input becomes visible after the previous one is connected, up to a maximum of 50 inputs.</td><td style="word-wrap: break-word;">[{'template': {'pdb': 'ATOM ...', 'chain_id': 'A'}}]</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_list</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A single list containing all provided Boltz objects, with one level of flattening applied if inputs were lists.</td><td style="word-wrap: break-word;">[{'protein': {'id': ['A'], 'sequence': 'MA...'}}, {'ligand': {'id': ['L'], 'smiles': 'CCO'}}, {'constraint': {'type': 'distance', 'chains': ['A', 'L'], 'threshold': 5.0}}, {'template': {'pdb': 'ATOM ...', 'chain_id': 'A'}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Progressive inputs**: input_2 through input_50 are hidden until the previous input is connected.
- **One-level flattening**: If an input is a list, its items are extended into the combined list; nested lists deeper than one level are not fully flattened.
- **Type-agnostic combination**: Each input can be any Boltz object type (sequence, constraint, template, property) or a list of such objects.
- **Ordering preserved**: Items are added to the combined list in the order of inputs (input_1, input_2, ...).
- **At least one input required**: The node raises an error if no inputs are provided.
- **Maximum inputs**: Supports up to 50 inputs.

## Troubleshooting
- **Error: 'At least one input is required'**: Ensure at least input_1 is connected and non-empty.
- **Unexpected nesting in output**: The node only flattens one level. If you pass nested lists (e.g., a list of lists), pre-flatten or ensure each input is either a single object or a flat list.
- **Inputs not appearing**: Additional inputs only appear after connecting the previous input. Connect input_1 first, then input_2 becomes available, and so on.
- **Mixed or invalid items**: Provide valid Boltz objects from builder nodes. If downstream nodes fail, verify each item structure matches expectations for sequences, constraints, templates, or properties.
