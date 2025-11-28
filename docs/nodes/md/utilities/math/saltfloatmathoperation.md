# Float Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs common mathematical operations on floating-point numbers. Supports binary operations (add, subtract, multiply, divide, modulo, power) and unary operations on the first input (sqrt, abs). Includes safety checks for invalid cases like division/modulo by zero and square root of negative numbers, returning 0.0 in those cases.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltfloatmathoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to compute a single floating-point result from one or two float inputs within a workflow. Typical use cases include parameter calculations, normalization steps, or building simple arithmetic pipelines. Choose the operation and provide inputs; for unary operations (sqrt, abs), only the first input is used.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">First float operand. Used as the base value for all operations; for sqrt and abs this is the only value used.</td><td style="word-wrap: break-word;">9.0</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Second float operand. Used for binary operations (add, subtract, multiply, divide, modulo, power). Ignored for sqrt and abs.</td><td style="word-wrap: break-word;">3.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING (one of: add, subtract, multiply, divide, modulo, power, sqrt, abs)</td><td style="word-wrap: break-word;">Specifies which mathematical operation to perform.</td><td style="word-wrap: break-word;">divide</td></tr>
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
- **Input ranges**: a and b accept values between -1e10 and 1e10 with a step of 0.001.
- **Unary operations**: sqrt and abs only use input 'a'; 'b' is ignored.
- **Division/modulo by zero**: If b is 0 for divide or modulo, the node returns 0.0.
- **Square root domain**: If a is negative for sqrt, the node returns 0.0.
- **Power operation**: Large exponents may overflow or produce very large values; ensure inputs are within practical bounds.
- **Unknown operation**: If an unsupported operation is provided, the node returns 0.0.
- **Category**: SALT/Utility/Math

## Troubleshooting
- **Result is 0.0 unexpectedly**: Check if you attempted division/modulo by zero, sqrt of a negative number, or selected an invalid operation.
- **Output seems unchanged for unary operations**: Remember that sqrt and abs ignore 'b' and operate only on 'a'.
- **Operation selection not available**: Ensure the operation value is one of the allowed options: add, subtract, multiply, divide, modulo, power, sqrt, abs.
- **Precision issues**: Floating-point arithmetic can introduce rounding errors; consider rounding downstream if exact decimal representation is needed.
