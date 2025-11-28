# Int Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs basic integer arithmetic on two inputs. Supports addition, subtraction, multiplication, integer division, modulo, and exponentiation. Division and modulo handle zero safely by returning 0.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/logic-utility/saltintmathoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need deterministic integer math within a workflow, such as counters, index calculations, loop increments/decrements, or preparing integer parameters for other nodes. Choose the desired operation and provide two integer operands.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">First integer operand.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Second integer operand.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Mathematical operation to perform on the two integers. Options: add, subtract, multiply, divide (integer division), modulo, power.</td><td style="word-wrap: break-word;">subtract</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The result of the selected integer operation.</td><td style="word-wrap: break-word;">7</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Division uses integer division (a // b).
- Division by zero and modulo by zero are guarded and will return 0.
- If an unknown operation is provided or an error occurs, the node returns 0.
- Input values are integers and are constrained by very large min/max bounds; ensure values are within the UI-allowed range.
- Exponentiation (power) can grow quickly; use with care.

## Troubleshooting
- Result is 0 unexpectedly: Verify that b is not 0 for divide/modulo and that the chosen operation is correct.
- Non-integer behavior observed: Ensure both inputs are integers; this node performs integer math (e.g., divide truncates toward negative infinity).
- Overflow or extremely large results: Check inputs for large exponents or products; consider smaller values or a float operation node if appropriate.
- Wrong operation applied: Reconfirm the 'operation' choice from the list [add, subtract, multiply, divide, modulo, power].
