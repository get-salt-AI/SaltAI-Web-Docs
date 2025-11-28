# Accumulation Tail

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Returns the tail of an accumulation by removing the first element. If the accumulation has zero or one items, it returns an empty accumulation. It preserves the accumulation structure expected by other list/accumulation nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulationtailnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to iterate through or process an accumulation by progressively dropping the head element. Commonly chained after an accumulation-producing node and before further list operations (e.g., length checks, get/set item, or converting back to a plain list).

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An accumulation object containing a list under the 'accum' key. The head (first item) will be removed.</td><td style="word-wrap: break-word;">{"accum": ["a", "b", "c"]}</td></tr>
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
<tr><td style="word-wrap: break-word;">tail</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The accumulation with its first element removed. If fewer than two elements exist, returns an empty accumulation.</td><td style="word-wrap: break-word;">{"accum": ["b", "c"]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- If the input is not a valid accumulation or lacks the 'accum' key, the node returns an empty accumulation: {"accum": []}.
- If the input accumulation has length 0 or 1, the result is {"accum": []}.
- The declared output type is WILDCARD, but the actual structure returned is an accumulation-like object with an 'accum' list. Ensure downstream nodes expect an accumulation.
- This node does not mutate the input; it returns a new accumulation object.

## Troubleshooting
- Output is empty unexpectedly: Verify the input is a proper accumulation object with an 'accum' list and that it contains at least two items.
- Downstream type mismatch: Some nodes may expect an ACCUMULATION type explicitly. If needed, confirm compatibility or convert using related nodes.
- Need a plain list: If you require a native list, pass the output to 'Accumulation to List' to extract the list from the accumulation.
