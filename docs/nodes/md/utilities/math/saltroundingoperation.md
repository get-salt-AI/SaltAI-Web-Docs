# Rounding Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs numeric rounding on a single value. Supports floor, ceil, standard rounding to a specified number of decimal places, and truncation toward zero. Always returns a floating-point result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltroundingoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to transform a numeric value to a rounded form for downstream logic, formatting, or thresholding. Typical flows include cleaning sensor outputs, preparing values for display, or enforcing numeric constraints before comparisons.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The numeric value to be rounded. Accepted range is approximately -1e10 to 1e10.</td><td style="word-wrap: break-word;">3.14159</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">['floor', 'ceil', 'round', 'trunc']</td><td style="word-wrap: break-word;">The rounding method to apply. 'floor' rounds down, 'ceil' rounds up, 'round' rounds to nearest with optional decimals, 'trunc' removes the fractional part toward zero.</td><td style="word-wrap: break-word;">round</td></tr>
<tr><td style="word-wrap: break-word;">decimals</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of decimal places to round to. Used only when operation is 'round'. Valid range is 0–10.</td><td style="word-wrap: break-word;">2</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The rounded numeric result as a float.</td><td style="word-wrap: break-word;">3.14</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Decimals usage**: The 'decimals' input only affects the 'round' operation; it is ignored for 'floor', 'ceil', and 'trunc'.
- **Return type**: The node always returns a FLOAT, even when the mathematical result is an integer (e.g., 3.0).
- **Rounding behavior**: 'round' uses standard rounding with ties-to-even behavior (banker's rounding) when exactly between two values.
- **Bounds**: Inputs outside the range [-1e10, 1e10] are not accepted by the UI constraints.
- **Error handling**: If an unknown operation is provided or an error occurs, the node returns 0.0.

## Troubleshooting
- **Unexpected 0.0 output**: Ensure 'operation' is one of ['floor', 'ceil', 'round', 'trunc'] and 'value' is a valid number.
- **Decimals not applied**: Confirm 'operation' is set to 'round'; other operations ignore the 'decimals' input.
- **Tie rounding differs from expectations**: For .5 cases, 'round' uses ties-to-even. If you need always-round-half-up, consider adjusting the value before rounding.
- **Result is float instead of int**: This is by design. If an integer is needed, convert downstream as appropriate.
