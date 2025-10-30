# Accumulation to List

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts an ACCUMULATION object into a standard list. It extracts the internal sequence of items from the accumulation container and returns them as a plain list, preserving order. If the input is invalid or empty, it returns an empty list.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/Logic/Flow Control/Accumulation/SaltAccumulationToListNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have been collecting items with accumulation-based nodes and need a native list for downstream operations such as iteration, selection, transformation, or output. Typically placed after nodes like Accumulate or List to Accumulation to expose the collected items as a list for further processing.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The accumulation container holding a sequence of items under its internal storage. Usually produced by Accumulate or List to Accumulation nodes.</td><td style="word-wrap: break-word;">ACCUMULATION object produced by Accumulate (e.g., {"accum": ["item A", "item B", 3]})</td></tr>
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
<tr><td style="word-wrap: break-word;">list</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A standard list of the accumulated items. Items are returned in insertion order.</td><td style="word-wrap: break-word;">["item A", "item B", 3]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node returns an empty list if the input is not a valid ACCUMULATION or has no items.
- Item order is preserved from how they were added to the accumulation.
- If earlier accumulation steps skipped None values (per their logic), those None items will not appear; this node itself does not apply additional filtering.
- The output type is wildcard (*) but practically represents a list of items that may be of mixed types.

## Troubleshooting
- If the output is an empty list unexpectedly, ensure the input is a valid ACCUMULATION and contains items (verify upstream nodes like Accumulate or List to Accumulation).
- If downstream nodes expect a specific element type, ensure earlier steps added the correct types to the accumulation, since the list may contain mixed types.
- If you see unexpected items, check upstream accumulation logic to confirm None-skipping or other behaviors that may affect collected items.
