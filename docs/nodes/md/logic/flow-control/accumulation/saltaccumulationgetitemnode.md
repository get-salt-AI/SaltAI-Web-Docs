# Accumulation Get Item

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Retrieves a single element from an accumulation at a specified index. If the index is invalid or the accumulation is malformed, the node returns a null item. The output type matches the type of the stored element.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulationgetitemnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to extract a specific element from an accumulation created or transformed by other list/accumulation nodes (e.g., Accumulate, Accumulation Head/Tail, List to Accumulation). Provide the accumulation and the zero-based index of the item you want to retrieve.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The accumulation object to read from, typically produced by other accumulation or list nodes.</td><td style="word-wrap: break-word;"><accumulation-object></td></tr>
<tr><td style="word-wrap: break-word;">index</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based position of the item to retrieve. Supports typical integer indexing.</td><td style="word-wrap: break-word;">0</td></tr>
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
<tr><td style="word-wrap: break-word;">item</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The element at the provided index within the accumulation. Returns null if the index is out of range or the accumulation is invalid.</td><td style="word-wrap: break-word;">example-item</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Indexing is zero-based**: Index 0 returns the first element.
- **Out-of-range indices return null**: If the index is invalid, the node outputs a null item.
- **Output type is dynamic**: The output type follows the element type stored in the accumulation.
- **Provide a valid accumulation**: Ensure the input is a proper accumulation object produced by compatible nodes.
- **Negative indices**: Depending on the accumulation's underlying list behavior, negative indices may access from the end; if unsupported or out of range, null is returned.

## Troubleshooting
- **Got null output**: Verify the index is within the valid range of the accumulation and that the accumulation contains elements.
- **Unexpected type**: Ensure the accumulation contains the expected element types; this node returns whatever type is stored at the index.
- **Accummulation input not accepted**: Confirm the input comes from accumulation-compatible nodes (e.g., Accumulate or List to Accumulation) and not a raw list.
- **Off-by-one errors**: Remember indexing starts at 0; adjust the index accordingly.
