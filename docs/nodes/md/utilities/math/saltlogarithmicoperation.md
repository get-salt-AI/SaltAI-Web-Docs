# Logarithmic Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs logarithmic and exponential calculations on a single numeric value. Supports custom-base logarithms, natural/base-10/base-2 logarithms, and exponential functions (e^x and 2^x). The node validates inputs and returns 0.0 for invalid domain values.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltlogarithmicoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to transform numeric data using logs or exponentials—for example for data normalization, feature engineering, growth/decay modeling, or scaling values. Choose the operation type, set the input value, and if using a custom log, specify the base. Connect the output to downstream math, control, or analysis nodes.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The numeric input to transform. Must be > 0 for logarithmic operations; any real number is allowed for exponential operations.</td><td style="word-wrap: break-word;">100.0</td></tr>
<tr><td style="word-wrap: break-word;">base</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The logarithm base used only when operation is 'log'. Must be > 0 and not equal to 1. Ignored for ln, log10, log2, exp, and exp2.</td><td style="word-wrap: break-word;">10.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">ENUM[log, ln, log10, log2, exp, exp2]</td><td style="word-wrap: break-word;">Selects which operation to perform: 'log' (custom base), 'ln' (natural log), 'log10' (base 10), 'log2' (base 2), 'exp' (e^value), or 'exp2' (2^value).</td><td style="word-wrap: break-word;">log</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed result of the selected logarithmic or exponential operation. Returns 0.0 if inputs are outside the valid domain or an error occurs.</td><td style="word-wrap: break-word;">2.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- For 'log' (custom base), value must be > 0 and base must be > 0 and != 1; otherwise the node returns 0.0.
- For 'ln', 'log10', and 'log2', value must be > 0; non-positive values return 0.0.
- For 'exp' and 'exp2', large positive inputs may overflow to very large numbers; large negative inputs approach 0.
- The 'base' input is ignored for ln, log10, log2, exp, and exp2.
- On invalid inputs or unknown operations, the node returns 0.0 to signal a problem.

## Troubleshooting
- Unexpected 0.0 result: Check that 'value' > 0 for logarithms and that 'base' > 0 and != 1 when using 'log'.
- Result seems off when using ln/log10/log2: Ensure you selected the correct operation; the 'base' input is ignored for these.
- Overflow or extremely large values with exp/exp2: Reduce 'value' magnitude or switch to a logarithmic transform to stabilize the range.
- Precision issues with very large/small inputs: Consider scaling inputs prior to applying logs/exponentials to keep values within a manageable range.
