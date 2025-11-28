# Accumulation to List

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts an Accumulation object into a plain list. This node extracts the internal accumulated items (kept under the 'accum' container) and returns them as a single list output. It preserves the order in which items were accumulated.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulationtolistnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after one or more accumulation steps (e.g., after using Accumulate or other Accumulation manipulation nodes) when you need a standard list to feed into downstream nodes that expect a list rather than an Accumulation. Typical workflow: build up items with Accumulate → optionally inspect or modify → convert to list here → process the list elsewhere.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The Accumulation object holding items in its internal 'accum' list. Usually produced by Accumulate or List to Accumulation.</td><td style="word-wrap: break-word;">{'accum': ['item_a', 'item_b', 'item_c']}</td></tr>
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
<tr><td style="word-wrap: break-word;">accumulated_list</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A plain list of the accumulated items extracted from the provided Accumulation.</td><td style="word-wrap: break-word;">['item_a', 'item_b', 'item_c']</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Output is a single list**: The output socket emits one list containing all items; it is not a list of multiple outputs.
- **Type-agnostic items**: Items inside the list can be of any type; this node does not enforce a specific element type.
- **Graceful fallback**: If the input is missing or not a valid Accumulation, the node returns an empty list.
- **Order preserved**: Items appear in the list in the same order they were accumulated.
- **Category**: SALT/Logic/Lists

## Troubleshooting
- **Empty list output**: Ensure the input comes from a node that outputs ACCUMULATION and that it contains items. If the Accumulation is uninitialized or malformed, this node returns an empty list.
- **Type mismatch**: If downstream nodes reject the output, check their expected element types. While this node allows any element type, downstream nodes may require specific types.
- **Unexpected single item wrapping**: If previous steps did not produce a proper Accumulation, convert with List to Accumulation first to ensure correct structure.
