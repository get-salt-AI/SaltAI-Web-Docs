# Logarithmic Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs logarithmic and exponential calculations on a single numeric input. Supports custom-base logarithm, natural/base-10/base-2 logarithms, and e^x or 2^x exponentials. The node validates domain constraints (e.g., positive inputs for logs, valid base) and returns 0.0 on invalid inputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltlogarithmicoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need logarithmic or exponential transformations in a math or data-processing pipeline. Typical workflows include scaling values into log space, converting between different log bases, or applying exponential growth/decay functions before further processing.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The numeric input for the selected operation. Must be > 0 for all logarithm operations.</td><td style="word-wrap: break-word;">100.0</td></tr>
<tr><td style="word-wrap: break-word;">base</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The base used only when operation is 'log' (custom-base logarithm). Must be > 0 and not equal to 1.</td><td style="word-wrap: break-word;">10.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select the operation to perform. Options: 'log' (custom base), 'ln' (natural log), 'log10' (base-10 log), 'log2' (base-2 log), 'exp' (e^value), 'exp2' (2^value).</td><td style="word-wrap: break-word;">log</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed result of the chosen logarithmic or exponential operation. Returns 0.0 if inputs are invalid or an error occurs.</td><td style="word-wrap: break-word;">2.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Domain constraints**: For 'log', 'ln', 'log10', and 'log2', value must be > 0. For 'log' specifically, base must be > 0 and not equal to 1.
- **Base input usage**: The 'base' input is only used for 'log' (custom-base). It is ignored for 'ln', 'log10', 'log2', 'exp', and 'exp2'.
- **Error handling**: On invalid parameters or runtime errors (e.g., overflow), the node returns 0.0.
- **Exponential growth**: 'exp' and 'exp2' can grow very large quickly; extreme inputs may lead to overflow and a 0.0 return.
- **Precision**: Results depend on floating-point precision; tiny numerical differences may occur.

## Troubleshooting
- **Result is 0.0 for logarithm**: Ensure value > 0. For 'log', also ensure base > 0 and base != 1.
- **Unexpected 0.0 on exponentials**: The input may be too large, causing overflow. Try reducing the magnitude of 'value'.
- **Base seems to have no effect**: The 'base' field is only used when operation is 'log'. It is ignored for 'ln', 'log10', 'log2', 'exp', and 'exp2'.
- **Incorrect results**: Verify the selected 'operation' matches your intent (e.g., 'log10' vs 'log2') and that 'value' meets the domain constraints.
