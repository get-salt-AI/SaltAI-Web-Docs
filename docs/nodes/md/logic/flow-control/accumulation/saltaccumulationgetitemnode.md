# Accumulation Get Item

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node extracts a single element from an Accumulation container using a zero-based index. It expects a valid Accumulation structure (as produced by nodes like Accumulate or List to Accumulation) and returns the value stored at the specified position. If the index is out of range or the Accumulation is malformed, it safely returns a null item instead of failing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulationgetitemnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an Accumulation of items and need to pick out one specific element for further processing. A common pattern is: build the Accumulation with Accumulate or convert an existing list via List to Accumulation, optionally inspect its size with Accumulation Get Length, then use Accumulation Get Item to access a particular position by index. This node typically sits mid-pipeline: upstream nodes provide or build the ACCUMULATION (for example, SaltAccumulateNode, SaltListToAccumulationNode), and downstream nodes consume the extracted item for operations like branching logic, transformation, or feeding into tools and models. In workflows with loops, you can iterate over indices (0 to length-1) and repeatedly call this node to access each element in turn. Prefer using this node instead of manually indexing container-like structures, as it handles errors by returning a null value instead of interrupting the run.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An Accumulation object containing an ordered sequence of items. This is typically created by nodes such as Accumulate or List to Accumulation. Internally it behaves like a list stored under an "accum" field; it must be a valid Accumulation for indexing to succeed.</td><td style="word-wrap: break-word;">{'accum': ['Step 1 summary: patient reported mild chest pain.', 'Step 2 summary: ECG results normal.', 'Step 3 summary: recommended follow-up in 3 months.']}</td></tr>
<tr><td style="word-wrap: break-word;">index</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based index of the item to retrieve from the Accumulation. 0 refers to the first element, 1 to the second, and so on. Valid range is from 0 up to (length - 1); if the index is out of bounds or negative, the node will not throw an error but will return a null item instead.</td><td style="word-wrap: break-word;">1</td></tr>
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
<tr><td style="word-wrap: break-word;">item</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The element retrieved from the Accumulation at the given index. The type matches whatever was stored in the Accumulation (text, numbers, images, structured objects, and similar). If the index is invalid or the Accumulation is malformed, this output will be null.</td><td style="word-wrap: break-word;">Step 2 summary: ECG results normal.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Accessing an item by index is an O(1) operation on the underlying list, so it is efficient even for large Accumulations.
- **Limitations**: If the accumulation input does not follow the expected Accumulation structure, for example if it is missing the internal accum list, the node will return a null item instead of the desired value.
- **Behavior**: Indexing uses zero-based semantics. Requesting the first element requires index 0, not 1.
- **Behavior**: When an index is out of range or invalid, the node logs an internal error and returns null rather than stopping the workflow, so you may want to explicitly check for null downstream.

## Troubleshooting
- **Item is always null**: If item is consistently null, verify that the upstream node is producing a proper ACCUMULATION, for example from Accumulate or List to Accumulation, and that there is at least one element stored before accessing an index.
- **Unexpected index results**: If you retrieve a different element than expected, confirm that you are using zero-based indices, with 0 for the first item, 1 for the second, and so on, and that earlier steps are adding items in the order you expect.
- **Intermittent null values in loops**: When iterating over an Accumulation, make sure your loop counter never reaches the Accumulation length. Valid indices go from 0 to length-1. Use Accumulation Get Length to determine the upper bound before looping.
