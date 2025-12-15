# Boltz List Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines multiple BOLTZ objects (sequences, constraints, templates, properties) into a single ordered list. Accepts up to 50 inputs and flattens any inputs that are already lists. If no inputs are provided, it raises an error.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzlistcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to gather all BOLTZ components you’ve built into one list before assembling a final YAML or submitting for prediction. Typically, connect outputs from Boltz Sequence/Constraint/Template/Property builders; if any of those already output lists, they will be flattened into a single combined list.

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
<tr><td style="word-wrap: break-word;">input_1</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">First BOLTZ object or list of objects to include. Enables subsequent inputs after connection.</td><td style="word-wrap: break-word;">A single sequence object or a list like [sequence1, sequence2]</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Second BOLTZ object or list of objects. Hidden until input_1 is connected.</td><td style="word-wrap: break-word;">A constraint object or [constraint1, constraint2]</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_list</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A single flattened list containing all provided BOLTZ objects in the order they were connected.</td><td style="word-wrap: break-word;">[sequence1, sequence2, constraint1, templateA, propertyX]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Input fan-out: if an input provides a list, those items are appended individually; single objects are appended as-is.
- At least one input is required; otherwise the node raises an error.
- Inputs are revealed progressively: input_2 is hidden until input_1 is connected, and so on.
- The node does not validate object types; ensure you only combine valid BOLTZ objects intended for downstream nodes.
- Order is preserved based on input index, then the order within any list passed into that input.
- Maximum of 50 inputs; consider pre-combining if you need more.

## Troubleshooting
- Error: 'At least one input is required' — Connect at least one BOLTZ object to input_1.
- Inputs after input_1 are not visible — Connect input_1 first; each subsequent input becomes available only after the previous is set.
- Output contains nested lists — This node flattens only the top-level input lists; ensure you do not pass lists-of-lists unless nesting is intended to remain inside items.
- Unexpected item order — The output list preserves the order of inputs (input_1 before input_2, etc.) and the internal order of any lists provided.
- Exceeded maximum inputs — Only input_1 through input_50 are processed; pre-merge items using upstream nodes if needed.
