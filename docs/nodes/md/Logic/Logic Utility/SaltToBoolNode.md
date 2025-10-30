# To Bool

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts any incoming value to a boolean. For tensor inputs, it returns False only when all elements are exactly zero; otherwise True. For non-tensor values, it uses standard truthiness conversion and can optionally invert the result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Logic/Logic Utility/SaltToBoolNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a clear True/False decision from a value of any type. Common in control flows, gating logic, and branching where inputs may be numbers, strings, lists, or tensors. Toggle 'invert' to flip the final boolean.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to convert to boolean. Tensors are True if any element is non-zero; other types follow standard truthiness (e.g., empty collections and zero are False, non-empty and non-zero are True).</td><td style="word-wrap: break-word;">0, 1, "", "hello", [], [1], a tensor of all zeros, a tensor with any non-zero element</td></tr>
<tr><td style="word-wrap: break-word;">invert</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If True, the computed boolean result is inverted.</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">The boolean outcome after conversion (and optional inversion).</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Tensor behavior**: Returns False only if the tensor's min and max are both exactly 0 (i.e., all elements are zero). Any non-zero element yields True.
- **NaN in tensors**: Tensors containing NaN will not have min/max equal to 0 and will evaluate to True.
- **Non-tensor truthiness**: Uses standard truthiness rules. Examples: 0, 0.0, empty string, empty list/dict evaluate to False; non-zero numbers and non-empty strings/collections evaluate to True.
- **String caveat**: The string "false" (or any non-empty string) evaluates to True. Parse strings first if you need semantic boolean parsing.
- **Fallback on conversion errors**: If a non-tensor value cannot be converted (rare), the node defaults to True before applying 'invert'.
- **Invert option**: When enabled, it flips the final boolean result.

## Troubleshooting
- **Unexpected True for text 'false'**: Convert the string to a real boolean before this node (e.g., map 'true'/'false' to booleans) since non-empty strings are truthy.
- **Tensor evaluates True when expected False**: Ensure the tensor truly contains only zeros; even a single non-zero (or NaN) element makes it True.
- **Result seems reversed**: Check if 'invert' is enabled.
- **Got False due to error**: While rare, internal errors log and return False. Validate your input type and shape, and retry.
