# Float Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs basic arithmetic and scalar operations on floating-point numbers. Supports binary operations (add, subtract, multiply, divide, modulo, power) and unary operations applied to the first operand (sqrt, abs). Includes safety checks for division/modulo by zero and square root of negative values, returning 0.0 for invalid cases.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltfloatmathoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to compute numeric results in workflows that require floating-point math. Provide two float inputs and select an operation; for unary operations (sqrt, abs), only the first input is used. It’s commonly placed before thresholding, branching conditions, normalization, or parameter calculations.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">First float operand. For unary operations (sqrt, abs), this is the only value used.</td><td style="word-wrap: break-word;">9.0</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Second float operand used by binary operations (add, subtract, multiply, divide, modulo, power). Ignored for unary operations (sqrt, abs).</td><td style="word-wrap: break-word;">3.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Mathematical operation to perform. One of: add, subtract, multiply, divide, modulo, power, sqrt, abs.</td><td style="word-wrap: break-word;">divide</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed result of the selected operation. Returns 0.0 for invalid operations or unsafe inputs (e.g., divide by zero, modulo by zero, sqrt of negative).</td><td style="word-wrap: break-word;">3.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Operations and behavior: add (a + b), subtract (a - b), multiply (a * b), divide (a / b), modulo (a % b), power (a ** b), sqrt (sqrt(a)), abs (abs(a)).
- Unary operations (sqrt, abs) ignore input b and operate only on a.
- Division by zero and modulo by zero return 0.0.
- Square root of a negative value returns 0.0.
- Input ranges are constrained to approximately [-1e10, 1e10] with a step precision of 0.001.
- Modulo uses floating-point remainder semantics.

## Troubleshooting
- Result is 0.0 unexpectedly: Check for division/modulo by zero or sqrt on a negative number.
- Wrong value when using sqrt or abs: Ensure you are setting the intended value in 'a'; 'b' is ignored for these operations.
- Unexpected modulo behavior: Remember that modulo on floats returns the floating-point remainder, which may differ from integer modulo expectations.
- Out-of-range input warnings or clamping: Keep inputs within the allowed range (approximately [-1e10, 1e10]) to avoid issues.
