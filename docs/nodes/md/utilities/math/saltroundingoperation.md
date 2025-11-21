# Rounding Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Applies a selected rounding method to a numeric value. Supports floor, ceil, round (with configurable decimal places), and truncation toward zero. Returns a float result and falls back to 0.0 on invalid inputs or errors.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltroundingoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to normalize or quantize numeric values before further processing, comparisons, or display. Typical workflows include clamping precision (round with decimals), converting to whole-number boundaries (floor/ceil), or removing fractional parts without bias (trunc).

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The number to be rounded. Accepts values in the range -1e10 to 1e10.</td><td style="word-wrap: break-word;">3.14159</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">['floor', 'ceil', 'round', 'trunc']</td><td style="word-wrap: break-word;">The rounding method to apply. floor rounds down, ceil rounds up, round uses standard rounding with the specified decimals, trunc removes the fractional part toward zero.</td><td style="word-wrap: break-word;">round</td></tr>
<tr><td style="word-wrap: break-word;">decimals</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of decimal places to keep when operation is 'round'. Ignored by floor, ceil, and trunc. Allowed range is 0 to 10.</td><td style="word-wrap: break-word;">2</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The rounded value as a float. For floor/ceil/trunc, the integer result is returned as a float.</td><td style="word-wrap: break-word;">3.14</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Decimals usage**: The 'decimals' input only affects the 'round' operation; it is ignored for 'floor', 'ceil', and 'trunc'.
- **Python rounding behavior**: 'round' follows typical rounding rules with the specified decimals; behavior is consistent with standard numeric rounding and may round halves to the nearest even digit in some environments.
- **Type of output**: Even when the result is a whole number, the node returns a FLOAT.
- **Input ranges**: 'value' must be between -1e10 and 1e10; 'decimals' must be between 0 and 10.
- **Error handling**: On invalid inputs or unknown operations, the node returns 0.0.
- **Truncation**: 'trunc' removes the fractional part toward zero (e.g., -3.7 becomes -3).

## Troubleshooting
- **Result is 0.0 unexpectedly**: Ensure 'operation' is one of ['floor','ceil','round','trunc'] and inputs are within allowed ranges.
- **Decimals not applied**: The 'decimals' input only applies when 'operation' is 'round'.
- **Unexpected rounding of .5 values**: Be aware of standard rounding behavior; if you need a different rounding convention, adjust your value or workflow accordingly.
- **Out-of-range inputs**: If 'value' is outside [-1e10, 1e10] or 'decimals' is outside [0, 10], adjust inputs to valid ranges.
