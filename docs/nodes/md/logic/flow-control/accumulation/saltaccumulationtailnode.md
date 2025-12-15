# Accumulation Tail

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Returns the tail of an accumulation by removing its first element. If the accumulation has zero or one element, it returns an empty accumulation. The node is defensive: on invalid inputs or errors, it yields an empty accumulation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulationtailnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when processing a sequence stored in an accumulation and you need everything except the first element (e.g., iterative processing, recursion, or stepping through a queue-like structure). Typically paired with Accumulation Head/Accumulate/To List nodes for list-style workflows.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An accumulation object containing a list under the key 'accum'.</td><td style="word-wrap: break-word;">{'accum': [10, 20, 30]}</td></tr>
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
<tr><td style="word-wrap: break-word;">tail</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The tail of the input accumulation (an accumulation object with all elements except the first). If the input has <= 1 elements or is invalid, returns an empty accumulation.</td><td style="word-wrap: break-word;">{'accum': [20, 30]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- If the input accumulation is empty or has a single element, the output is an empty accumulation: {"accum": []}.
- If the input is not a valid accumulation object, the node returns an empty accumulation.
- Downstream nodes should expect an accumulation-like object on the output, even though the declared output type is WILDCARD.

## Troubleshooting
- Output is empty unexpectedly: Ensure the input is a proper accumulation object shaped like {"accum": [...]}, and that it contains at least two elements.
- Downstream type mismatch: Some nodes may expect an ACCUMULATION type explicitly; insert a conversion or ensure the receiving node can accept the output as an accumulation.
- Errors in logs and empty output: Validate that the input is not None and follows the expected structure {"accum": list}.
