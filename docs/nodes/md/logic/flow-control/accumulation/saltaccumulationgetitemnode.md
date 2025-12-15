# Accumulation Get Item

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves a single item from an accumulation by its index. If the index is valid, it returns the element at that position; if it is invalid, it returns a null value.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulationgetitemnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to access a specific element from a previously built accumulation (e.g., after collecting items across steps). Typical workflow: build or receive an accumulation, then use this node to fetch the element at a given zero-based index for downstream processing.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The accumulation object that holds a sequence of items collected from prior steps.</td><td style="word-wrap: break-word;">An accumulation produced by 'Accumulate' or 'List to Accumulation'</td></tr>
<tr><td style="word-wrap: break-word;">index</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based index of the item to retrieve. Negative indices select from the end (e.g., -1 for the last item).</td><td style="word-wrap: break-word;">0</td></tr>
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
<tr><td style="word-wrap: break-word;">item</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The item at the specified index within the accumulation. Type depends on what was stored in the accumulation.</td><td style="word-wrap: break-word;">42</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Indexing**: Uses zero-based indexing; negative indices are allowed to access from the end.
- **Out-of-range handling**: If the index is out of bounds or the accumulation is invalid, the output will be null.
- **Type variability**: The output type is dynamic and matches the type of the item stored in the accumulation.

## Troubleshooting
- **Output is null**: The index may be out of range or the accumulation is empty. Verify the accumulation length and adjust the index.
- **Unexpected item type**: Accumulations can contain mixed types. Ensure the downstream node can accept the retrieved item's type.
- **Wrong element returned**: Remember indexing is zero-based; confirm the intended position and adjust the index accordingly.
