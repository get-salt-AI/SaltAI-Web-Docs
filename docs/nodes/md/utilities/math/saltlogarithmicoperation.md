# Logarithmic Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs logarithmic and exponential calculations on a single numeric value. Supports custom-base logarithms, natural and common logs, and exponential functions with bases e and 2. Inputs are validated and the node returns 0.0 for invalid or out-of-domain values.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltlogarithmicoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to transform values using logarithms (e.g., compression, normalization, scale adjustments) or exponentials (e.g., growth modeling, re-scaling). Typical workflows include data preparation, numeric feature engineering, or mathematical pipelines where you must switch between log and exp domains.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The primary input to operate on. For logarithms, this must be greater than 0. For exponentials, any float is allowed.</td><td style="word-wrap: break-word;">100.0</td></tr>
<tr><td style="word-wrap: break-word;">base</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Base used only when operation is 'log' (custom-base logarithm). Must be > 0 and not equal to 1. Ignored for ln, log10, log2, exp, and exp2.</td><td style="word-wrap: break-word;">10.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Select the operation to apply. Options: 'log' (custom base), 'ln' (natural log), 'log10' (base-10 log), 'log2' (base-2 log), 'exp' (e^value), 'exp2' (2^value).</td><td style="word-wrap: break-word;">log10</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed numeric result of the selected operation, or 0.0 if inputs are invalid or out of domain.</td><td style="word-wrap: break-word;">2.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Domain requirements**: For logarithms, value must be > 0. For custom-base 'log', base must be > 0 and not equal to 1.
- **Ignored base**: The 'base' input is ignored for 'ln', 'log10', 'log2', 'exp', and 'exp2'.
- **Safety behavior**: If inputs are invalid (e.g., non-positive value for log), the node returns 0.0 and logs a warning.
- **Precision and range**: Extremely large inputs for exponential operations can overflow or produce very large results; the node will attempt the computation but may return 0.0 on error.
- **Input constraints**: UI limits typically constrain value and base to [0.001, 1e10] for safer interaction.

## Troubleshooting
- **Result is 0.0 unexpectedly**: Check that 'value' > 0 for all logarithmic operations. For 'log' also ensure 'base' > 0 and not equal to 1.
- **Base seems to have no effect**: The 'base' input only affects the 'log' operation. It is not used for 'ln', 'log10', 'log2', 'exp', or 'exp2'.
- **Overflow or extreme values**: If using 'exp' or 'exp2' with large 'value', try reducing the magnitude or normalizing inputs to avoid overflow.
- **Wrong operation selected**: Verify the 'operation' choice matches your intent (e.g., use 'log10' for common log rather than 'log' with base 10 if you don't need a custom base).
