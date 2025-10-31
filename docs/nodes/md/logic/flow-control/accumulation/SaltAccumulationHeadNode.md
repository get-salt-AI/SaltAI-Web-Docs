# Accumulation Head

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Extracts the first element (head) from an accumulation. If the accumulation is empty or invalid, it outputs None. The node is type-agnostic and returns the element in its original type.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/SaltAccumulationHeadNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you are incrementally building a list of values with Accumulate (or converting a list to an accumulation) and need to read the first item in that collection. Typical in iterative workflows, conditional branching, or when peeking at the current leading value without modifying the accumulation.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An accumulation object containing a list of values under the internal key. Usually produced by Accumulate or List to Accumulation.</td><td style="word-wrap: break-word;">{'accum': ['first', 'second', 'third']}</td></tr>
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
<tr><td style="word-wrap: break-word;">head</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The first element of the accumulation. Returns None if the accumulation is empty or not properly formed.</td><td style="word-wrap: break-word;">first</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Returns None for empty inputs**: If the accumulation has no items or is malformed, the output will be None.
- **Type-agnostic output**: The output type matches the type of the first element in the accumulation (string, number, image, etc.).
- **Requires a valid accumulation**: Ensure the input is produced by compatible nodes like Accumulate or List to Accumulation.
- **Non-mutating**: This node does not modify the accumulation; it only reads the first element.

## Troubleshooting
- **Got None instead of a value**: Verify the accumulation is not empty and is a valid accumulation object (e.g., produced by Accumulate).
- **Unexpected type on output**: Remember the output type is the same as the first element's type. Check the data being added to the accumulation.
- **Head doesn't match expected item**: Ensure items are added in the intended order to the accumulation (new items are appended, so the head is the earliest added item unless the accumulation was constructed differently).
