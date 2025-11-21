# Float Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs selected mathematical operations on floating-point numbers. Supports arithmetic (add, subtract, multiply, divide, modulo, power) and unary operations on the first operand (sqrt, abs). Protects against invalid inputs by returning 0.0 for division/modulo by zero and square root of negative values.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltfloatmathoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to compute float-based math within a workflow, such as combining parameter values, normalizing results, or preparing intermediate numeric values. Choose the operation and supply operands a and b; for unary operations like sqrt and abs, only the 'a' input is used.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">First float operand. Used for all operations; for unary operations (sqrt, abs) this is the only value considered.</td><td style="word-wrap: break-word;">9.0</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Second float operand. Used for binary operations (add, subtract, multiply, divide, modulo, power). Ignored for sqrt and abs.</td><td style="word-wrap: break-word;">3.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Mathematical operation to perform. One of: add, subtract, multiply, divide, modulo, power, sqrt, abs.</td><td style="word-wrap: break-word;">divide</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed result of the selected mathematical operation.</td><td style="word-wrap: break-word;">3.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Operand ranges**: Inputs are clamped to a practical range of approximately -1e10 to 1e10 with a step of 0.001.
- **Unary vs binary**: 'sqrt' and 'abs' only use 'a'; 'b' is ignored for these operations.
- **Division/modulo by zero**: If b is 0 for divide or modulo, the node returns 0.0.
- **Square root domain**: If 'a' is negative for 'sqrt', the node returns 0.0.
- **Power operation**: Large exponents may overflow or produce very large/small results.
- **Result type**: Output is always a FLOAT, even when the mathematical result is an integer value.

## Troubleshooting
- **Unexpected 0.0 result**: Check for divide/modulo by zero or square root of a negative number; adjust inputs accordingly.
- **Result ignores 'b'**: Ensure the operation isn't 'sqrt' or 'abs', which are unary and only use 'a'.
- **Inaccurate decimals or precision issues**: Floating-point arithmetic can introduce rounding artifacts; consider scaling inputs or rounding results downstream.
- **Modulo behaving unexpectedly with negatives**: Modulo follows Python's float modulo rules; verify signs of 'a' and 'b' or adjust inputs.
- **Overflow or Infinity/Nan in power**: Reduce magnitude of operands or exponents when using 'power'.
