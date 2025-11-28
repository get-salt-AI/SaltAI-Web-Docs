# Accumulation Head

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Returns the first element (head) from an accumulation. If the accumulation is empty or invalid, it outputs None. Works with any item type and preserves the original type of the head element.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulationheadnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after building an accumulation (e.g., via Accumulate or List to Accumulation) when you need to extract just the first item. Typical in list-processing workflows where you split a collection into head and tail or need to peek at the first value for control logic.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An accumulation object representing a list-like collection produced by other Salt list/accumulation nodes.</td><td style="word-wrap: break-word;">{'accum': ['hello', 'world']}</td></tr>
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
<tr><td style="word-wrap: break-word;">head</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The first element of the provided accumulation. Returns None if the accumulation is empty or invalid.</td><td style="word-wrap: break-word;">hello</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The output type is WILDCARD and matches the type of the first item in the accumulation.
- If the accumulation is empty or not a valid accumulation object, the node returns None.
- Provide a proper ACCUMULATION object (typically produced by Accumulate, List to Accumulation, or related nodes) rather than a raw list.

## Troubleshooting
- Head is None: Ensure the accumulation is not empty and is a valid ACCUMULATION object.
- Unexpected output type: The node preserves the type of the first element; verify the items being accumulated are of the expected type.
- Invalid input error or silent None: Feed in an ACCUMULATION from compatible nodes instead of a plain list or mismatched structure.
