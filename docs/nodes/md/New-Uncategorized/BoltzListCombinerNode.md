# Boltz List Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines multiple BOLTZ objects into a single flat list. It accepts up to 50 inputs of any compatible BOLTZ type (e.g., sequences, constraints, templates, properties). If an input is itself a list, it is flattened into the combined output; single items are appended as-is.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/BoltzListCombinerNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to merge several BOLTZ components into one ordered collection before passing them to downstream nodes (e.g., prediction or configuration builders). Connect your first BOLTZ object to input_1, then additional inputs will progressively reveal (input_2, input_3, etc.). The node preserves the order based on input indices.

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
<tr><td style="word-wrap: break-word;">input_1</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">First BOLTZ object or list of BOLTZ objects to include. This initial connection enables subsequent inputs.</td><td style="word-wrap: break-word;">{'sequence': {'name': 'A', 'seq': 'MKT...'}}</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_list</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A single, flat list containing all provided BOLTZ objects in input order.</td><td style="word-wrap: break-word;">[{'sequence': {'name': 'A', 'seq': 'MKT...'}}, {'sequence': {'name': 'B', 'seq': 'GGK...'}}, {'constraint': {'type': 'distance', 'pairs': [['A:45', 'B:12', 8.0]]}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **At least one input is required**: The node will error if no inputs are connected.
- **Flattening behavior**: If an input is a list, its elements are expanded into the combined output; nested lists are effectively flattened one level.
- **Progressive input reveal**: input_2 becomes available after connecting input_1, and so on up to input_50.
- **Order is preserved**: Items are appended in ascending input index order (input_1, then input_2, etc.).
- **Type flexibility**: Each input accepts any BOLTZ object type or a list of them; mixing types is supported.
- **Hidden system fields**: 'id' and 'prompt' are managed by the system and do not require user input.

## Troubleshooting
- **Error: 'At least one input is required'**: Connect at least one BOLTZ object to input_1.
- **Inputs not appearing (input_2, input_3, ...)**: Ensure input_1 is connected; subsequent inputs reveal only after the previous slot is filled.
- **Unexpected nesting in output**: Provide lists or items directly; the node flattens one level of lists. If you pass a list of lists, inner lists will remain nested.
- **Incorrect item order**: Verify which input index each object is connected to; the output list follows input_1 through input_50 order.
