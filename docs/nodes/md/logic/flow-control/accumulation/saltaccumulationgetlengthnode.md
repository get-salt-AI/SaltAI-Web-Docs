# Accumulation Get Length

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Returns the number of items stored inside an Accumulation. If the provided input is not a valid Accumulation, the node safely returns 0.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulationgetlengthnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need the count of items that have been collected through Accumulation-based workflows (e.g., after iteratively adding items with Accumulate). Commonly used for conditional logic, branching, or validating that an Accumulation contains items before further processing.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The Accumulation object to measure. This should be created by Accumulate or related Accumulation nodes and internally holds a list of collected items.</td><td style="word-wrap: break-word;">{'accum': ['a', 'b', 'c']}</td></tr>
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
<tr><td style="word-wrap: break-word;">length</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The number of items contained in the input Accumulation.</td><td style="word-wrap: break-word;">3</td></tr>
</tbody>
</table>
</div>

## Important Notes
- If the input is missing or not a valid Accumulation, the node returns 0.
- This node only reads the Accumulation; it does not modify it.
- Designed to work with Accumulation objects produced by nodes like Accumulate, Accumulation Head/Tail, and Accumulation Get/Set Item.

## Troubleshooting
- Length is always 0: Ensure the input is a valid Accumulation object produced by compatible nodes and that it contains items.
- Unexpected length value: Verify that items were actually added to the Accumulation before this node executes.
- Type errors upstream: Confirm that the upstream node outputs an ACCUMULATION type, not a plain list or other data structure.
