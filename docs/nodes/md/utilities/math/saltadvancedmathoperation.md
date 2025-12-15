# Advanced Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs a set of advanced mathematical operations on a single number, with optional use of a second integer for GCD/LCM. Supported operations include absolute value, square root, cube root, factorial (of the integer part), greatest common divisor, and least common multiple.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltadvancedmathoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need single-input advanced math utilities or integer-based pair operations (GCD/LCM). Typical workflow: feed a numeric value, choose the operation, and provide the secondary integer only when using GCD or LCM. The node returns the result as a float.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Primary numeric input. Used by all operations. For factorial/GCD/LCM, its absolute integer part is used internally.</td><td style="word-wrap: break-word;">16.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">["abs", "sqrt", "cbrt", "factorial", "gcd", "lcm"]</td><td style="word-wrap: break-word;">Selects the operation to perform.</td><td style="word-wrap: break-word;">sqrt</td></tr>
<tr><td style="word-wrap: break-word;">second_value</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Secondary integer used only for GCD and LCM. Ignored for other operations.</td><td style="word-wrap: break-word;">12</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed result of the selected operation.</td><td style="word-wrap: break-word;">4.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Absolute value (abs)**: Returns the absolute value of 'value'.
- **Square root (sqrt)**: Requires value >= 0. If value is negative, the node returns 0.0.
- **Cube root (cbrt)**: Only supports non-negative inputs; negative inputs will result in 0.0.
- **Factorial**: Uses the absolute integer part of 'value' (floor of abs(value)). Values that would produce excessively large factorials (greater than 170!) return 0.0.
- **GCD/LCM**: Both operate on the absolute integer parts of 'value' and 'second_value'.
- **LCM with zero**: If the integer part of 'value' is 0, the LCM result is 0.
- **Output type**: Results are returned as FLOAT, even when the underlying operation is integer-based (factorial, gcd, lcm).
- **Second value range**: 'second_value' is constrained to integers between 1 and 1000.

## Troubleshooting
- **Got 0.0 unexpectedly**: Check for invalid inputs: negative values for sqrt or cbrt, factorial too large (>170!), or other input constraints.
- **GCD/LCM results seem off**: Remember the node uses the absolute integer parts of inputs. Verify 'value' and 'second_value' after conversion to integers.
- **LCM returned 0**: This occurs if the integer part of 'value' is 0.
- **Need cube root of a negative number**: This node does not support negative cube roots; use a method that handles signed cube roots.
