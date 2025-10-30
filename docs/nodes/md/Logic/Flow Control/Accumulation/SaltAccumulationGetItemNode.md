# Accumulation Get Item

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves a single item from an accumulation at the specified index. If the index is invalid or the accumulation is missing the expected data, it returns None instead of failing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/Logic/Flow Control/Accumulation/SaltAccumulationGetItemNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an accumulation (built by Salt list/accumulation nodes) and need to extract a specific element by its position. Commonly placed after building or transforming an accumulation to pick a particular entry for further processing.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The accumulation structure containing an ordered collection of items. It must be a valid accumulation produced by related accumulation nodes.</td><td style="word-wrap: break-word;">An accumulation containing items, e.g., a collection of images or texts.</td></tr>
<tr><td style="word-wrap: break-word;">index</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based position of the item to retrieve. Negative indices select from the end if supported by the underlying accumulation.</td><td style="word-wrap: break-word;">0</td></tr>
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
<tr><td style="word-wrap: break-word;">item</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The item at the specified index from the accumulation. The type matches whatever is stored at that position (image, text, number, etc.). Returns None if not found.</td><td style="word-wrap: break-word;">A single image, a string, a number, or None</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Indexing**: Uses zero-based indexing; negative indices are supported if the underlying list supports them.
- **Invalid access**: If the index is out of range or the accumulation is missing its data slot, the output will be None.
- **Type propagation**: Output type is dynamic (WILDCARD) and depends on the stored item; ensure downstream nodes can accept that type.
- **Input validity**: Pass only valid accumulations produced by Salt accumulation nodes; other structures may cause errors.

## Troubleshooting
- **Output is None**: The requested index may be out of bounds. Check the accumulation length first using 'Accumulation Get Length' and adjust the index.
- **Unexpected type downstream**: The retrieved item type may not match what the next node expects. Confirm the item type or insert conversion nodes as needed.
- **Errors with invalid input**: If you see errors, ensure the input is a proper ACCUMULATION (not a plain list or dict) created by the Salt accumulation nodes.
- **Negative index not working as expected**: Verify the accumulation contains a list-like structure. If needed, confirm valid indices via length and adjust accordingly.
