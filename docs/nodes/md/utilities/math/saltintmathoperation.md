# Int Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs basic arithmetic on two integers. Supports addition, subtraction, multiplication, integer division, modulo, and exponentiation. Division and modulo by zero are guarded and return 0.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltintmathoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need integer arithmetic in a workflow, such as initializing counters, accumulating totals, or computing loop indices. Connect integer-producing nodes to inputs a and b, select the desired operation, and route the single integer result to downstream nodes (e.g., conditions, loops, or outputs).

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">First integer operand. Accepts any 64-bit range integer.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Second integer operand. Accepts any 64-bit range integer.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING (choice: add, subtract, multiply, divide, modulo, power)</td><td style="word-wrap: break-word;">Specifies which arithmetic operation to perform on a and b. divide uses integer division (floor).</td><td style="word-wrap: break-word;">multiply</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The integer result of the selected operation.</td><td style="word-wrap: break-word;">15</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Division behavior**: The divide operation performs integer division (a // b). For negative values, this is floor division.
- **Zero handling**: If b is 0 for divide or modulo, the node returns 0.
- **Large numbers**: power can produce very large integers; ensure downstream nodes can handle big values.
- **Type expectations**: Inputs must be integers; provide conversions upstream if starting from strings or floats.

## Troubleshooting
- **Result is 0 unexpectedly**: Check if b is 0 for divide or modulo; the node returns 0 in these cases.
- **Non-integer inputs rejected**: Ensure upstream nodes output INT. Add conversion steps if necessary.
- **Unexpected division result with negatives**: Remember integer division floors toward negative infinity (e.g., -3 // 2 = -2).
- **Performance issues on power**: Very large exponents can be slow or produce huge numbers; constrain inputs or switch to float operations if appropriate.
