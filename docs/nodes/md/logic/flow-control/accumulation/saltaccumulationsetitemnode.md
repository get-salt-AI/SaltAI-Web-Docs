# Accumulation Set Item

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Updates an element at a specific index within an Accumulation. It performs an in-place modification of the internal list and returns the same Accumulation object reference. If the index is invalid or the structure is not an Accumulation, it safely returns the original Accumulation unchanged.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulationsetitemnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to overwrite a particular element inside an existing Accumulation, such as correcting a value or replacing a placeholder. Typically placed after building an Accumulation (e.g., via Accumulate or List to Accumulation) and before nodes that consume the updated list.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The Accumulation to modify. Must be a valid Accumulation produced by compatible nodes.</td><td style="word-wrap: break-word;"><accumulation_object></td></tr>
<tr><td style="word-wrap: break-word;">index</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based position of the element to set. Negative indices are supported (Python-style) and count from the end.</td><td style="word-wrap: break-word;">2</td></tr>
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The new value to place at the specified index. Should match the expected item type used within the Accumulation.</td><td style="word-wrap: break-word;">example item</td></tr>
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
<tr><td style="word-wrap: break-word;">accumulation</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The same Accumulation object after the attempted update. If an error occurs (e.g., index out of range), the original Accumulation is returned unchanged.</td><td style="word-wrap: break-word;"><accumulation_object></td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node mutates the Accumulation in place; downstream branches sharing the same Accumulation reference will see the change.
- Negative indices are allowed (e.g., -1 refers to the last element).
- If the index is out of range or the input is not a valid Accumulation, the node logs an error internally and returns the original Accumulation without changes.
- For best results, ensure the value type is consistent with other elements in the Accumulation, especially if downstream nodes expect a specific type.

## Troubleshooting
- Item did not change: Verify the index is within the valid range of the Accumulation length.
- Type-related issues downstream: Ensure the 'value' you set matches the expected type used by subsequent nodes.
- Unexpected no-op: Confirm the input is a valid Accumulation produced by the proper nodes, not a plain list or other structure.
- Changes appearing in multiple places: Remember this node updates the Accumulation in place; if the same Accumulation is reused in multiple branches, all will reflect the update.
