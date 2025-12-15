# Float Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs common mathematical operations on floating-point numbers. Supports binary operations (add, subtract, multiply, divide, modulo, power) and unary operations (sqrt and abs applied to the first input). Includes safety checks for division/modulo by zero and square root of negative numbers.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltfloatmathoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to compute a float result from one or two float inputs in a workflow. Select the desired operation and supply operands a and b. For unary operations (sqrt, abs), only the first operand a is used but both inputs must still be provided.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">First float operand. Used as the left-hand side for binary operations and as the operand for unary operations (sqrt, abs). Accepts values in the range [-1e10, 1e10].</td><td style="word-wrap: break-word;">9.0</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Second float operand. Used for binary operations (add, subtract, multiply, divide, modulo, power). Ignored for unary operations (sqrt, abs). Accepts values in the range [-1e10, 1e10].</td><td style="word-wrap: break-word;">3.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">['add','subtract','multiply','divide','modulo','power','sqrt','abs']</td><td style="word-wrap: break-word;">Mathematical operation to perform. Binary operations use both a and b. Unary operations (sqrt, abs) apply only to a.</td><td style="word-wrap: break-word;">divide</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed floating-point result of the selected operation.</td><td style="word-wrap: break-word;">3.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Division by zero**: If b is 0 during divide, the node returns 0.0 and logs a warning.
- **Modulo by zero**: If b is 0 during modulo, the node returns 0.0 and logs a warning.
- **Square root domain**: If a is negative for sqrt, the node returns 0.0 and logs a warning.
- **Unary operations**: For sqrt and abs, only a is used; b is ignored but still required.
- **Value ranges**: Inputs a and b accept values from -1e10 to 1e10 with step 0.001.

## Troubleshooting
- **Unexpected 0.0 result**: Check if you divided or modulo by zero, or attempted sqrt on a negative number; these cases return 0.0 with a warning.
- **Wrong result for unary operations**: Ensure you're interpreting b correctly—b is ignored for sqrt and abs.
- **Overflow or extreme values**: If results seem off with very large magnitudes, confirm a and b fall within the accepted range [-1e10, 1e10].
- **Unknown operation**: If the operation string is outside the allowed set, the node returns 0.0 and logs a warning; verify the operation value.
