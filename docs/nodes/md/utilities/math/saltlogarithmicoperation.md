# Logarithmic Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs logarithmic and exponential calculations on a single numeric input. Supports custom-base logarithm, natural log, base-10 and base-2 logs, as well as e^x and 2^x. The node validates inputs for domain constraints (e.g., positive values for logs) and returns 0.0 on invalid inputs or errors.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltlogarithmicoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to transform a numeric value with logarithmic or exponential functions, such as feature scaling, signal transformation, or mathematical workflows. Select the desired operation and supply the value (and base for custom logs). Chain this node before or after other numeric processing nodes where non-linear scaling is required.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The numeric input to transform. Must be greater than 0 for logarithmic operations. For exponential operations, any real value is accepted (very large magnitudes may overflow).</td><td style="word-wrap: break-word;">42.0</td></tr>
<tr><td style="word-wrap: break-word;">base</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The base used only when operation is 'log' (custom-base logarithm). Must be > 0 and not equal to 1. Ignored for ln, log10, log2, exp, and exp2.</td><td style="word-wrap: break-word;">10.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">["log", "ln", "log10", "log2", "exp", "exp2"]</td><td style="word-wrap: break-word;">Select the operation: 'log' (custom base), 'ln' (natural log), 'log10', 'log2', 'exp' (e^value), or 'exp2' (2^value).</td><td style="word-wrap: break-word;">log2</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed result of the selected logarithmic or exponential operation. Returns 0.0 for invalid inputs or runtime errors.</td><td style="word-wrap: break-word;">5.3923174228</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Domain constraints**: For 'log', 'ln', 'log10', and 'log2', the input value must be > 0. For 'log' (custom base), the base must be > 0 and not equal to 1.
- **Error handling**: If inputs are invalid (e.g., value <= 0 for logs, base <= 0, or base == 1) or if a computation error occurs (e.g., overflow), the node outputs 0.0.
- **Base input usage**: The 'base' input is used only for the 'log' operation and is ignored for all others.
- **Range and precision**: Very large positive values for exponential operations can overflow, yielding 0.0 due to error handling. Very small values may underflow toward 0.

## Troubleshooting
- **Result is 0.0 unexpectedly**: Ensure value > 0 for logarithmic operations and, for 'log', verify base > 0 and base != 1.
- **Base seems to have no effect**: The 'base' field only applies to the 'log' operation; it is ignored for 'ln', 'log10', 'log2', 'exp', and 'exp2'.
- **Large exponent returns 0.0**: The computation may overflow. Use smaller input values or normalize your input before applying 'exp' or 'exp2'.
- **Negative or zero value for logs**: Logs of non-positive numbers are invalid; adjust your data or add a preprocessing step to clamp/filter inputs.
