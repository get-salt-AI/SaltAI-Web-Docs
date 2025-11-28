# To Bool

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts an incoming value to a boolean. Tensors are treated as False only if all elements are zero; otherwise True. An optional invert flag flips the final result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/logic-utility/salttoboolnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a boolean signal from any prior output to drive logic branches or condition checks. Typical workflows include validating non-empty strings, non-zero numbers, or non-all-zero tensors before routing execution.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Any input value to evaluate as a boolean. For tensors, the result is False if all elements are zero, otherwise True. For other types, standard truthiness is used.</td><td style="word-wrap: break-word;">A tensor, number, string, list, or any object</td></tr>
<tr><td style="word-wrap: break-word;">invert</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If True, inverts the computed boolean result.</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">The computed boolean value after optional inversion.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Tensors are considered False only when both their max and min are 0 (i.e., all elements are zero). Any non-zero element makes it True.
- Non-tensor values use normal truthiness rules (e.g., empty string/list -> False; non-empty -> True; zero -> False; non-zero -> True).
- If the node cannot convert a non-tensor value to bool due to a ValueError or TypeError, it defaults to True.
- Any unexpected runtime error during processing results in False.
- Setting invert to True flips the final boolean outcome.

## Troubleshooting
- Result is False for a tensor you expected to be True: Ensure the tensor contains at least one non-zero element. All-zero tensors evaluate to False.
- Result is unexpectedly True for a custom object: If the object's boolean conversion raises an error, the node defaults to True. Implement a __bool__ method or provide a different input type.
- Result is False due to an internal error: Check upstream inputs for invalid or unsupported values (e.g., tensors with problematic states). The node returns False on unhandled exceptions.
- Performance concerns with very large tensors: The check uses min/max; if performance is an issue, consider pre-reducing or summarizing the tensor before this node.
