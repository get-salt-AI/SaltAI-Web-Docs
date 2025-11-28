# Accumulation Set Item

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Updates an item at a specific index within an accumulation. It writes the provided value into the accumulation's internal list at the given index and returns the updated accumulation. If the index is invalid or the accumulation is malformed, it returns the original accumulation unchanged.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulationsetitemnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to modify an element within an existing accumulation, such as updating a computed result, replacing a placeholder, or correcting a value in a workflow that builds or refines lists over time. Typically paired with nodes that create or transform accumulations, and often used inside iterative or conditional logic branches.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The accumulation object containing an ordered list of items to be updated.</td><td style="word-wrap: break-word;">{"accum": ["a", "b", "c"]}</td></tr>
<tr><td style="word-wrap: break-word;">index</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based index of the item to update. Negative indices refer to items from the end (e.g., -1 is the last item).</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The new value to store at the specified index. Can be any supported type compatible with the accumulation's item type.</td><td style="word-wrap: break-word;">"z"</td></tr>
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
<tr><td style="word-wrap: break-word;">accumulation</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The accumulation after attempting to set the item at the specified index. If the operation fails (e.g., invalid index), the original accumulation is returned.</td><td style="word-wrap: break-word;">{"accum": ["a", "z", "c"]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Indexing**: Uses zero-based indexing; negative indices are allowed and reference from the end of the list.
- **Mutation behavior**: The accumulation is updated in place; downstream nodes will see the modified accumulation.
- **Failure handling**: If the index is out of range or the accumulation is missing its internal list, the node returns the input accumulation unchanged.
- **Type flexibility**: The value input is a wildcard; ensure it matches the expected type for consumers of the accumulation.
- **Structure expectation**: The accumulation must contain an internal list of items (typically accessible as "accum").

## Troubleshooting
- **Index out of range**: Verify the index is within the accumulation's length or use negative indices for items from the end. Consider checking length beforehand with an appropriate length node.
- **No change after execution**: This can occur if the index was invalid or the accumulation structure was malformed. Confirm the accumulation contains a valid list and the index is correct.
- **Unexpected item type errors downstream**: Ensure the 'value' you set matches what downstream nodes expect (e.g., do not mix text with image objects unless intended).
- **Concurrent modifications**: If multiple branches update the same accumulation reference, results may be unpredictable. Sequence updates or duplicate the accumulation before parallel edits.
