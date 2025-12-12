# Int Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs a mathematical operation on two integers and returns a single integer. Supports addition, subtraction, multiplication, integer division, modulo, and exponentiation. Division and modulo by zero are safely handled by returning 0.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/logic-utility/saltintmathoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need integer arithmetic in a workflow, such as counters, loop indices, pagination, or discrete transformations. Select the operation and provide integer inputs a and b. Note that divide performs integer (floor) division.

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
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Mathematical operation to apply to a and b.</td><td style="word-wrap: break-word;">divide</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The computed integer after applying the selected operation to a and b.</td><td style="word-wrap: break-word;">3</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Division uses integer division (floor), e.g., 10 / 3 returns 3.
- If b is 0 for divide or modulo, the node returns 0.
- Power can produce very large integers; be mindful of input sizes.
- On unknown operations or internal errors, the node returns 0.
- Inputs support very large negative and positive integers.

## Troubleshooting
- Result is 0 unexpectedly: Ensure b is not 0 for divide/modulo and that a valid operation is selected.
- Unexpected rounding in divide: Integer division truncates toward negative infinity; use a float math node if fractional results are needed.
- Extremely large results or slow execution: Limit input sizes for power operations.
- Output not updating: Verify that a, b, and operation are correctly set or connected.
