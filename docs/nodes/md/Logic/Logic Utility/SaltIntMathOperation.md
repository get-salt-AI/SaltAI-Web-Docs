# Int Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs a basic mathematical operation on two integers and returns a single integer result. Supports addition, subtraction, multiplication, integer division, modulo, and exponentiation. Includes safeguards for division/modulo by zero and unknown operations.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Logic/Logic Utility/SaltIntMathOperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to compute a simple integer result within a workflow, such as counters, loop indices, or discrete arithmetic. Provide two integer operands and select the desired operation; the node outputs a single INT suitable for downstream logic or calculations.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">First integer operand. Accepts large positive or negative integers within the node's configured range.</td><td style="word-wrap: break-word;">7</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Second integer operand. Its role depends on the selected operation (e.g., divisor, modulus, exponent).</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">['add', 'subtract', 'multiply', 'divide', 'modulo', 'power']</td><td style="word-wrap: break-word;">The mathematical operation to apply to a and b. 'divide' performs integer division, 'modulo' returns the remainder, and 'power' computes a**b.</td><td style="word-wrap: break-word;">divide</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The integer result of the selected operation. Division is integer (floor) division; modulo is the remainder.</td><td style="word-wrap: break-word;">2</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Division and modulo by zero are prevented; in such cases the node returns 0 and logs a warning.
- Division uses integer floor division semantics, which may differ from truncation for negative values.
- Very large exponents may produce extremely large integers; consider bounds to avoid performance issues.
- If an unknown operation is provided, the node returns 0 and logs a warning.
- Input ranges are constrained to large 64-bit-like limits in the UI; values outside may be clamped or rejected.

## Troubleshooting
- Result is 0 unexpectedly: Check if b was 0 for 'divide' or 'modulo', or if 'operation' was misspelled or unsupported.
- Negative division result looks unexpected: Remember that integer division uses floor semantics (e.g., -3 // 2 equals -2).
- Performance issues or slow execution: Large 'power' operations can be expensive; reduce operands or choose a different approach.
- Output not as expected after connecting upstream nodes: Verify that upstream values are integers and within the allowed range.
