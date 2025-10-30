# Accumulate

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds and updates an accumulation container by appending a new value each time it runs. It accepts any value type to add, and combines it with an existing accumulation or starts a new one if none is provided. None values are skipped, leaving the accumulation unchanged or initializing an empty one.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/Logic/Flow Control/Accumulation/SaltAccumulateNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to progressively collect values across your workflow, such as gathering items generated over multiple steps or iterations. Feed the prior 'accumulation' output back into the node while providing a new 'to_add' item each run. Pair with other accumulation nodes (e.g., Head, Tail, To List) for inspection and transformation.

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
<tr><td style="word-wrap: break-word;">to_add</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to append to the accumulation. Accepts any type. If None, the node does not add anything.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">accumulation</td><td>False</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An existing accumulation container to extend. If not provided, a new accumulation is created. If a non-ACCUMULATION value is supplied, it becomes the first item.</td><td style="word-wrap: break-word;">{'accum': ['a', 'b']}</td></tr>
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
<tr><td style="word-wrap: break-word;">accumulation</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The updated accumulation container with items under the 'accum' key in order of insertion.</td><td style="word-wrap: break-word;">{'accum': ['a', 'b', 'c']}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- ACCUMULATION is a special container shaped like a dictionary with key 'accum' holding a list of items.
- None inputs for 'to_add' are ignored. If no prior accumulation exists, the result is an empty accumulation.
- If 'accumulation' is provided as a non-ACCUMULATION value, it is treated as the first item and combined with 'to_add'.
- Item order is preserved: new items are appended to the end.
- On internal errors, the node returns an empty accumulation and logs the error.

## Troubleshooting
- Unexpected empty result: If 'to_add' is None and no prior accumulation is provided, the result is an empty accumulation by design.
- Not appending as expected: Ensure 'accumulation' is a valid ACCUMULATION object (e.g., {'accum': [...]}) to strictly append; otherwise, a non-ACCUMULATION input will be treated as the first list element.
- Type mixing concerns: This node accepts any value type; if downstream nodes expect a specific type, validate or convert items after accumulation.
- Missing items when passing None: None is intentionally skipped. Provide a valid value in 'to_add' to append.
