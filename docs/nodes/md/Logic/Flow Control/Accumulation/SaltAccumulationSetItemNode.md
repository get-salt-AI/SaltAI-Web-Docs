# Accumulation Set Item

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Updates a single element within an accumulation at a specified index. It modifies the accumulation in place and returns the updated accumulation. If the index is invalid or the accumulation is malformed, it returns the original accumulation unchanged.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/Logic/Flow Control/Accumulation/SaltAccumulationSetItemNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to replace an element inside an accumulation (a list-like container used across Salt nodes). Typical workflow: build or receive an accumulation, update one position with a new value, then pass the updated accumulation to subsequent nodes. Useful for correcting or refining list contents mid-graph.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The accumulation object containing an internal ordered collection of items. Usually produced by nodes like Accumulate or List to Accumulation.</td><td style="word-wrap: break-word;">{'accum': [1, 2, 3]}</td></tr>
<tr><td style="word-wrap: break-word;">index</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based index of the element to replace. Negative indices follow Python semantics (e.g., -1 targets the last element). Must be within bounds of the accumulation.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">The new value to set at the specified index. Should be compatible with downstream consumers of the accumulation.</td><td style="word-wrap: break-word;">updated item</td></tr>
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
<tr><td style="word-wrap: break-word;">accumulation</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The same accumulation object with the item at the given index replaced by the provided value.</td><td style="word-wrap: break-word;">{'accum': [1, 'updated item', 3]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **In-place modification**: The node mutates the provided accumulation. If the same accumulation is used elsewhere in parallel branches, those branches will observe the updated value.
- **Index bounds**: If the index is out of range or the accumulation is malformed, the node returns the original accumulation unchanged.
- **Value type**: While any value is accepted, ensure it matches expectations of nodes that will consume this accumulation.
- **Negative indices**: Negative indexing is supported (e.g., -1 for last element) as long as it resolves to a valid position.

## Troubleshooting
- **Item not updated**: Check that the index is within bounds of the accumulation length. Use Accumulation Get Length to verify size.
- **Downstream type errors**: Ensure the new value's type aligns with what subsequent nodes expect. Replace with a compatible type if needed.
- **Unexpected changes in other branches**: Because this node mutates in place, duplicate or clone your accumulation before branching if you need isolated edits.
- **Malformed accumulation**: Ensure the input is a valid accumulation produced by compatible nodes (e.g., Accumulate or List to Accumulation). If not, rebuild the accumulation before setting an item.
