# Int Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs a basic mathematical operation on two integers. Supports addition, subtraction, multiplication, integer division, modulo, and exponentiation. Handles divide/modulo by zero by returning 0 and logging a warning.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/logic-utility/saltintmathoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need simple arithmetic between two integer values in a workflow. Typical uses include counters, index calculations, loop arithmetic, and conditional evaluations where integer results are required.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">First integer operand. Supports very large positive or negative integers.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Second integer operand. Supports very large positive or negative integers.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">The arithmetic operation to apply to a and b. Options: add, subtract, multiply, divide (integer division), modulo, power.</td><td style="word-wrap: break-word;">divide</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The integer result of the selected operation. For divide, the result is integer (floor) division.</td><td style="word-wrap: break-word;">3</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Division is integer division using floor semantics; results with negative numbers are floored, not truncated toward zero.
- Divide and modulo by zero are guarded; the node returns 0 and logs a warning in these cases.
- If an unknown operation is provided, the node returns 0 and logs a warning.
- Exponentiation can produce very large numbers; extremely large exponents may be slow or resource-intensive.
- Using a negative exponent will mathematically yield a fractional result in Python, but the node declares an INT output; avoid negative exponents to keep results integral.
- Valid operations are exactly: add, subtract, multiply, divide, modulo, power.

## Troubleshooting
- Result is 0 unexpectedly: Check for divide/modulo by zero or an invalid operation string; both return 0 by design.
- Unexpected negative division result: Remember the node uses floor division; for negative operands, the quotient is floored (e.g., -3 // 2 = -2).
- Large or slow computation: Very large exponents can be expensive; reduce the exponent or reconsider the operation.
- Non-integer-like output expected from power: Avoid negative exponents; use non-negative integer exponents to keep the output integral.
- Operation not found in the dropdown: Ensure the operation is one of add, subtract, multiply, divide, modulo, power.
