# Logarithmic Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs logarithmic and exponential calculations on a single numeric value. Supports custom-base logarithm, natural log, base-10, base-2 logs, and exponential functions with base e or 2. Inputs are validated to avoid invalid domains (e.g., non-positive values for logs), returning 0.0 when invalid.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/SaltLogarithmicOperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need logarithmic transformations or exponential growth calculations in a workflow. Typical use cases include scaling values, converting between orders of magnitude, computing natural or base-specific logs, or generating exponential outputs for further math or control logic. Choose the operation and set the value (and base when using custom log).

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The numeric input to operate on. Must be > 0 for log operations; can be any float for exp operations.</td><td style="word-wrap: break-word;">8.0</td></tr>
<tr><td style="word-wrap: break-word;">base</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Base for the custom logarithm when operation is 'log'. Must be > 0 and not equal to 1. Ignored for ln, log10, log2, exp, and exp2.</td><td style="word-wrap: break-word;">2.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Select the operation: 'log' (custom base), 'ln' (natural log), 'log10', 'log2', 'exp' (e^value), or 'exp2' (2^value).</td><td style="word-wrap: break-word;">log2</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed result of the selected logarithmic or exponential operation.</td><td style="word-wrap: break-word;">3.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input domains**: For 'log' the value must be > 0, base > 0, and base != 1. For 'ln', 'log10', and 'log2', the value must be > 0.
- **Ignored base**: The 'base' input is only used for the 'log' operation; it is ignored for all other operations.
- **Out-of-range handling**: Invalid inputs or math errors return 0.0. Extremely large exponents may overflow and likewise return 0.0.
- **Precision**: Results are floating-point and subject to standard floating-point precision behavior.
- **Defaults and ranges**: UI constraints typically enforce value/base within [0.001, 1e10]; adjust upstream if smaller or negative values are needed for non-log operations.

## Troubleshooting
- **Result is 0.0 for logs**: Ensure 'value' > 0; for 'log', also ensure 'base' > 0 and 'base' != 1.
- **Unexpected 0.0 for exp**: The input may be too large causing overflow. Reduce 'value' or normalize your input before applying 'exp'/'exp2'.
- **Base seems to have no effect**: The 'base' parameter only affects the 'log' operation. For ln/log10/log2, it is intentionally ignored.
- **Precision discrepancies**: Small differences can occur due to floating-point arithmetic. If needed, round downstream using a rounding node.
