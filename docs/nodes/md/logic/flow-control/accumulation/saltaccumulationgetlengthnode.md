# Accumulation Get Length

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Returns the number of items currently stored in an Accumulation. If the input is not a valid Accumulation, it safely returns 0.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulationgetlengthnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to measure how many elements are in an Accumulation during list-building or iterative workflows. Commonly placed after nodes that create or modify an Accumulation (e.g., Accumulate, Accumulation Head/Tail, List to Accumulation) to branch logic based on size, validate loop progress, or guard against empty collections.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The Accumulation object whose element count should be computed. Must be produced by compatible Accumulation-related nodes.</td><td style="word-wrap: break-word;">{'accum': [1, 2, 3]}</td></tr>
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
<tr><td style="word-wrap: break-word;">length</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The number of items in the provided Accumulation. Returns 0 if the input is invalid or empty.</td><td style="word-wrap: break-word;">3</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The input must be a valid Accumulation object produced by Accumulation-compatible nodes.
- If the input is invalid or does not contain an 'accum' collection, the node returns 0.
- Ideal for control-flow checks, such as confirming that an Accumulation is non-empty before retrieving items.

## Troubleshooting
- Length is always 0: Ensure the input is connected to a node that outputs ACCUMULATION (not a plain list or other type).
- Unexpected small/large length: Verify upstream nodes are appending/removing items as intended before this node executes.
- Downstream errors using length: Confirm the length is greater than 0 before indexing into the Accumulation elsewhere.
