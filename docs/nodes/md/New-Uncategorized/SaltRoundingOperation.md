# Rounding Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Applies common numeric rounding to a single value. Supports floor (down), ceil (up), round (to nearest with optional decimals), and trunc (toward zero). Always outputs a float.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/SaltRoundingOperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to normalize or quantize a floating-point number before downstream logic (e.g., clamping values for UI display, index selection, thresholds). Choose the operation and optionally set decimals (only used by round).

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The numeric value to round. Accepted range is approximately -1e10 to 1e10.</td><td style="word-wrap: break-word;">3.14159</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">["floor", "ceil", "round", "trunc"]</td><td style="word-wrap: break-word;">The rounding operation to apply: floor (down), ceil (up), round (nearest), trunc (toward zero).</td><td style="word-wrap: break-word;">round</td></tr>
<tr><td style="word-wrap: break-word;">decimals</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of decimal places for the round operation (0–10). Ignored for floor, ceil, and trunc.</td><td style="word-wrap: break-word;">2</td></tr>
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
- **Decimals usage**: The decimals input only affects the round operation; it is ignored for floor, ceil, and trunc.
- **Output type**: Even integer-like results are returned as FLOAT (e.g., 3.0).
- **Rounding behavior**: The round operation uses standard nearest rounding to the specified decimals; ties may round to the nearest even value depending on the underlying rounding behavior.
- **Value limits**: Inputs are clamped to approximately -1e10 to 1e10; decimals are limited to 0–10.
- **Error fallback**: On invalid operation or runtime error, the node returns 0.0.

## Troubleshooting
- **Result is 0.0 unexpectedly**: Ensure the operation is one of floor, ceil, round, or trunc. Check that the input value is within the allowed range.
- **Decimals seems to have no effect**: Decimals only apply when operation is round. For floor, ceil, and trunc, decimals are ignored.
- **Unexpected tie rounding (e.g., 2.5)**: Be aware that ties may round to even; adjust your logic or add a small epsilon if a different tie strategy is needed.
- **Precision not achieved**: The node limits decimals to a maximum of 10; higher precision requests will not be applied.
