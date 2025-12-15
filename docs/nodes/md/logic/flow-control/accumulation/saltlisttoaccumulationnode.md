# List to Accumulation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts an input into an accumulation structure used by Salt list-logic nodes. If the input is a Python list, it is wrapped directly; if it is a single value, it is wrapped as a one-element accumulation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltlisttoaccumulationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to feed a standard list (or a single value) into nodes that operate on the Accumulation type (e.g., Head, Tail, Get Item, Set Item, Accumulate). It is typically placed just before list-processing nodes to standardize the data structure.

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
<tr><td style="word-wrap: break-word;">list</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The input to convert. If this is a list, its items become the accumulation contents. If this is a single value (any type), it becomes a single-item accumulation.</td><td style="word-wrap: break-word;">["a", "b", "c"]</td></tr>
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
<tr><td style="word-wrap: break-word;">accumulation</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An accumulation object containing the input items in order, suitable for all Accumulation-based list nodes.</td><td style="word-wrap: break-word;">{"accum": ["a", "b", "c"]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **List handling**: If the input is already a list, its elements are used as-is; the node does not flatten nested lists.
- **Single value handling**: If the input is a single value (not a list), it is wrapped into a one-item accumulation.
- **Type flexibility**: Accepts any item types (strings, numbers, images, custom objects); mixed-type lists are supported.
- **Error fallback**: On errors, the node returns an empty accumulation.
- **Pairing**: Designed to interoperate with Accumulation nodes like Accumulation Head, Tail, Get/Set Item, and Accumulation to List.

## Troubleshooting
- **Empty result**: If you receive an empty accumulation, ensure the input is provided and valid.
- **Unexpected nesting**: If you see nested lists inside the accumulation, verify that your input isn't already a list of lists; the node does not flatten.
- **Wrong downstream type**: If downstream nodes complain about types, confirm you converted raw lists to Accumulation with this node first.
- **Single value wrapped**: If you expected multiple items but got only one, check that your input is a list rather than a single value.
