# Accumulation Head

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Returns the first element (head) from an Accumulation. If the accumulation is empty or invalid, it outputs None. The node preserves the original item type (it is type-agnostic).

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulationheadnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to extract the first item from a growing collection of values built with Accumulation-compatible nodes. Commonly paired with Accumulate (to build the list) and Tail (to get the remainder), it helps in stepwise or iterative processing where you need the current first element.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An Accumulation object produced by nodes like Accumulate or List to Accumulation. Represents an ordered collection of items.</td><td style="word-wrap: break-word;"><ACCUMULATION from Accumulate or List to Accumulation></td></tr>
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
<tr><td style="word-wrap: break-word;">head</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The first item in the accumulation. Returns None if the accumulation is empty or invalid.</td><td style="word-wrap: break-word;">hello</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Type-preserving output**: The output item retains its original type (text, number, image reference, etc.).
- **Empty handling**: If the accumulation has no items, the output is None.
- **Input expectations**: This node expects a valid Accumulation object. Provide data from Accumulate or List to Accumulation nodes.
- **Order-sensitive**: The node returns the earliest added item (index 0) in the accumulation.
- **Safety behavior**: On errors or malformed input, the node returns None.

## Troubleshooting
- **Output is None**: Ensure the accumulation is not empty and is created by Accumulate or List to Accumulation. Verify that at least one item has been added.
- **Unexpected type at output**: The node returns the original item type. Confirm you are handling the correct type downstream.
- **Node appears to do nothing**: Confirm the upstream node actually outputs an ACCUMULATION type, not a plain list. If you have a list, convert it using List to Accumulation.
- **Order mismatch**: The node always returns the first element. If you need a different item, use Accumulation Get Item or process with Tail to access subsequent elements.
