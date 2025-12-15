# Accumulation to List

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts an Accumulation object into a plain list. If the input is a valid accumulation (a structure containing an 'accum' array), the node returns that array; otherwise it returns an empty list. Useful for exiting the accumulation workflow into standard list processing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulationtolistnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this after building up values with Accumulate or related accumulation nodes when you need a standard list for downstream list utilities, iteration, or display.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The accumulation object to convert. Must be an accumulation structure with an internal list of values.</td><td style="word-wrap: break-word;">{'accum': ['a', 'b', 'c']}</td></tr>
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
<tr><td style="word-wrap: break-word;">accumulated_list</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The plain list extracted from the accumulation. Returns an empty list if the input is not a valid accumulation.</td><td style="word-wrap: break-word;">['a', 'b', 'c']</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: Expects an accumulation object; typically a structure like {"accum": [...]}
- **Fallback behavior**: If the input is missing or malformed, the node returns an empty list instead of failing.
- **Output type**: The output is a standard list whose element type matches whatever was accumulated.
- **Not a list-output flag**: Although it returns a list, the node’s output is a single value that is itself a list (i.e., not a multi-output stream).

## Troubleshooting
- **Got an empty list unexpectedly**: Ensure the input is a valid accumulation object and that items were actually added (for example using Accumulate).
- **Downstream node rejects the output**: Confirm the downstream node accepts a list of the element type you accumulated.
- **Type confusion with nested lists**: If you accumulated lists themselves, the output will be a list of lists; verify this matches the downstream expectations.
