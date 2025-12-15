# To Bool

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts any input value into a boolean. For tensors, it returns False only if all values are zero; otherwise True. For non-tensor values, it follows standard truthiness rules and can optionally invert the result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/logic-utility/salttoboolnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a boolean flag derived from any upstream value to control logic flow. Common in conditional routing, gating, and enabling/disabling branches based on whether an input is effectively 'present' or 'non-zero'.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Any value to be evaluated for truthiness. Tensors are True if any element is non-zero; non-tensors use standard boolean conversion.</td><td style="word-wrap: break-word;">0, "", [1,2,3], {"k": "v"}, <tensor>, None</td></tr>
<tr><td style="word-wrap: break-word;">invert</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If True, flips the computed boolean result.</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">The boolean evaluation of the input (optionally inverted).</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- For tensors, the result is False only when all elements are exactly zero; any non-zero element makes it True.
- If the input is a non-tensor object that cannot be directly converted to a boolean (raises a conversion error), the node treats it as True.
- If an internal error occurs during evaluation, the node returns False.
- Using invert=True simply flips the final boolean result.

## Troubleshooting
- Unexpected True for a tensor: Ensure the tensor does not contain any non-zero values; any single non-zero element will yield True.
- Unexpected False result: If an internal error occurred (e.g., invalid or empty tensor for reduction), the node will return False; verify the input is valid.
- NaN values in tensors: Presence of NaNs does not count as zero and will typically yield True; sanitize the tensor if needed.
- Non-standard objects: If your custom object doesn't define clear truthiness and raises errors on bool conversion, it will be treated as True; consider providing a simpler proxy value.
