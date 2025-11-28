# List to Accumulation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts any input into an accumulation structure used by Salt list/accumulation nodes. If the input is already a Python list, it is used directly; otherwise, the value is wrapped into a single-item list and returned as an ACCUMULATION.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltlisttoaccumulationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to feed a list (or a single value) into nodes that expect an ACCUMULATION. Typical workflows include converting a prepared list into an ACCUMULATION for iteration, indexing, or further list operations with other accumulation nodes.

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
<tr><td style="word-wrap: break-word;">list</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The value or list to convert. If a Python list is provided, it becomes the accumulation contents; otherwise, the input is wrapped into a single-element list.</td><td style="word-wrap: break-word;">[1, 2, 3] or "hello world" or 42</td></tr>
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
<tr><td style="word-wrap: break-word;">accumulation</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An accumulation object with an 'accum' list containing the provided values.</td><td style="word-wrap: break-word;">{"accum": [1, 2, 3]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- If the input is a Python list, it is used as-is inside the accumulation. If not, the input is wrapped into a single-element list.
- Passing an ACCUMULATION object here will be treated as a non-list value and wrapped, resulting in an accumulation of the form {"accum": [{"accum": [...]}]}. Convert it to a plain list first if needed.
- Non-list iterables (e.g., tuples, generators, sets) will be wrapped as a single item; convert them to a list beforehand if you want their elements inside the accumulation.
- If the input is None, the output will be {"accum": [None]}.
- On internal errors, the node returns an empty accumulation: {"accum": []}.

## Troubleshooting
- Unexpected nested accumulation: You likely passed an ACCUMULATION. First convert it to a list using Accumulation to List, then pass the list here if needed.
- Tuple or set became a single item: Convert your input to a list before this node to spread elements inside the accumulation.
- Got {"accum": [None]}: Ensure your input isn't None. Provide a valid list or value.
- Empty output {"accum": []}: An internal error occurred or an invalid type was processed. Validate the input type and retry.
