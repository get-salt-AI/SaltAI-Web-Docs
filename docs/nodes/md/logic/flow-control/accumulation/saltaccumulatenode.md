# Accumulate

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds up an ordered list of values across steps or iterations. Each call appends the provided value to an accumulation container, gracefully handling first-time initialization and no-op behavior when the value to add is None.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulatenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to collect multiple values over time into a single list, such as aggregating results from a loop or conditional branch. Feed the 'accumulation' output back into the next call's 'accumulation' input to keep appending values, and supply the new item via 'to_add'.

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
<tr><td style="word-wrap: break-word;">to_add</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The next item to append to the accumulation. Accepts any type. If this is None, the node will not add anything and will forward the existing accumulation.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">accumulation</td><td>False</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The current accumulation container. If omitted or None, a new accumulation is started. Can be either a proper ACCUMULATION object or a raw value (which will be normalized).</td><td style="word-wrap: break-word;">{"accum": ["a", "b"]}</td></tr>
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
<tr><td style="word-wrap: break-word;">accumulation</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The updated accumulation container with the newly added item (if provided). Internally represented as an object with an 'accum' list.</td><td style="word-wrap: break-word;">{"accum": ["a", "b", "c"]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Skips None values**: If 'to_add' is None, the output simply forwards the existing accumulation (or creates an empty one if none exists).
- **Initialization behavior**: If no prior accumulation is provided, the node starts a new one containing only 'to_add' (unless 'to_add' is None, in which case it starts empty).
- **Flexible input**: If 'accumulation' is a proper ACCUMULATION (with an 'accum' list), the item is appended. If a raw value is provided as 'accumulation', it is normalized and the output becomes [accumulation, to_add].
- **Order preserving**: Items are appended in the order the node is executed.
- **Type-agnostic**: The accumulator can mix different value types in one list.
- **Error handling**: On internal errors, the node returns an empty accumulation {"accum": []}.

## Troubleshooting
- **Items are not appearing in the list**: Ensure 'to_add' is not None for the steps where you expect an item to be appended.
- **Accumulation resets unexpectedly**: Verify you are reconnecting the previous 'accumulation' output back into the next node's 'accumulation' input each iteration.
- **Unexpected first elements**: If you pass a non-ACCUMULATION value into 'accumulation', the node will treat it as the first element and append 'to_add'. Use a proper ACCUMULATION or the previous output to avoid this.
- **Empty result**: If you see {"accum": []}, you may have started with None and 'to_add' was None, or an error occurred upstream. Provide a valid 'to_add' or confirm prior steps.
- **Mixed types issues downstream**: Downstream nodes must handle mixed types if you accumulate heterogeneous items. Filter or standardize types before accumulation if needed.
