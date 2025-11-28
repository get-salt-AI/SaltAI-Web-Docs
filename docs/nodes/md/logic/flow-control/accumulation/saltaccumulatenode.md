# Accumulate

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Collects values into a growing list-like accumulation container across node executions. It appends the provided value to an existing accumulation or starts a new one. None inputs are safely ignored, preserving the current accumulation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulatenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to build up a sequence of values over time, such as collecting items across branches, iterations, or conditional flows. Feed the previous accumulation back into this node along with the next value to add. Downstream accumulation utility nodes (e.g., head, tail, to-list) can then consume or transform the accumulated data.

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
<tr><td style="word-wrap: break-word;">to_add</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to append to the accumulation. Accepts any type. If the value is None, it will be skipped.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">accumulation</td><td>False</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An existing accumulation container to append to. If not provided, a new accumulation is created.</td><td style="word-wrap: break-word;"><accumulation from a previous Accumulate node></td></tr>
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
<tr><td style="word-wrap: break-word;">accumulation</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The updated accumulation container including the newly added value (if provided).</td><td style="word-wrap: break-word;"><accumulation object containing previously collected items and the new item></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **None values are skipped**: If the input to_add is None, the node returns the existing accumulation unchanged (or an empty accumulation if none exists).
- **Type flexibility**: The node accepts any type for to_add; mixed types can coexist in the same accumulation.
- **Optional prior accumulation**: If no accumulation is provided, the node initializes a new one with the provided value (unless the value is None).
- **Error safety**: On unexpected errors, the node returns a valid but empty accumulation to prevent pipeline failures.

## Troubleshooting
- **Items not appearing in results**: Ensure to_add is not None; None values are intentionally ignored.
- **Downstream nodes cannot read the accumulation**: Confirm that downstream nodes expect an ACCUMULATION type rather than a plain list or single value.
- **Accumulation unexpectedly resets**: Make sure the prior accumulation output is correctly connected back into the accumulation input of the next Accumulate node call.
- **Mixed or unexpected item types**: Validate inputs to_add if consumers require homogeneous types; this node does not enforce type consistency.
