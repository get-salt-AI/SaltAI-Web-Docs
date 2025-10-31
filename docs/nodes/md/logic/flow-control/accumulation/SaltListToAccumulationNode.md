# List to Accumulation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts an input into an accumulation structure. If the input is a list, it becomes the accumulation contents directly; if it is a single value (not a list), it is wrapped into a one-element accumulation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/SaltListToAccumulationNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to initialize or reset an accumulation from an existing list, or when you want to standardize a single value into an accumulation for downstream list/accumulation operations. Commonly used before nodes that operate on the ACCUMULATION type (e.g., get length, get/set item, head/tail).

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
<tr><td style="word-wrap: break-word;">list</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to convert. If it is a Python list, its elements become the accumulation. Any non-list value is wrapped as a single element.</td><td style="word-wrap: break-word;">[1, 2, 3] or "hello"</td></tr>
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
<tr><td style="word-wrap: break-word;">accumulation</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An accumulation object containing the input elements under the 'accum' key.</td><td style="word-wrap: break-word;">{"accum": [1, 2, 3]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node treats only Python lists as lists; other iterables like tuples are considered single values and will be wrapped as one element.
- If the input is already an accumulation object (a dict with an 'accum' key), it will be wrapped as a single element rather than merged.
- Passing None results in an accumulation with a single None element.
- On error, the node returns an empty accumulation: {"accum": []}.

## Troubleshooting
- Input is a tuple and doesn't expand: Convert your tuple to a list before this node if you want its elements to populate the accumulation.
- Already have an ACCUMULATION and want a list: Use 'Accumulation to List' to extract elements instead of passing it here.
- Unexpected empty result: Check for upstream errors or invalid types; the node returns {"accum": []} on exceptions.
