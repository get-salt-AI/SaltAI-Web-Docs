# Rounding Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Applies a rounding function to a numeric value. Supports floor, ceil, round (with configurable decimal places), and truncation toward zero. Returns the result as a float and clamps behavior is consistent across operations.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltroundingoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to normalize or discretize numeric values for downstream nodes, such as converting continuous outputs to fixed precision, enforcing integer-like values, or simplifying values for thresholds and comparisons. Typical in data preparation, control flows, and formatting numeric outputs.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The numeric value to be processed. Accepted range is approximately -1e10 to 1e10.</td><td style="word-wrap: break-word;">3.14159</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">The rounding operation to perform. One of: floor, ceil, round, trunc.</td><td style="word-wrap: break-word;">round</td></tr>
<tr><td style="word-wrap: break-word;">decimals</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of decimal places used only when operation is 'round'. Ignored for floor, ceil, and trunc. Range 0–10.</td><td style="word-wrap: break-word;">2</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The rounded result as a float.</td><td style="word-wrap: break-word;">3.14</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Decimals is only applied when operation is 'round'; it is ignored for 'floor', 'ceil', and 'trunc'.
- Output is always a float, even when the result is an integer (e.g., floor(5.9) returns 5.0).
- If an unknown operation is provided or an error occurs, the node returns 0.0.
- Input constraints: value ~[-1e10, 1e10], decimals [0, 10].
- Trunc truncates toward zero (e.g., -3.7 -> -3.0).

## Troubleshooting
- Result is 0.0 unexpectedly: Ensure 'operation' is one of floor, ceil, round, trunc and that 'value' is a valid number.
- Decimals not affecting the result: Confirm the operation is 'round'; other operations ignore 'decimals'.
- Unexpected sign behavior with negatives: Remember 'trunc' moves toward zero; use 'floor' for negative values to round down (more negative).
- Precision concerns when rounding: For binary floating-point values, slight representation errors may occur; adjust 'decimals' or pre-scale if exact decimal rounding is critical.
