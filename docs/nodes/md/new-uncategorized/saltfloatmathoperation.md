# Float Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs basic mathematical operations on floating-point values. Supports addition, subtraction, multiplication, division, modulo, power, square root, and absolute value. Includes safety checks for division/modulo by zero and square root of negative numbers, returning 0.0 in those cases.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltfloatmathoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to compute a single float result from two float inputs in a workflow (e.g., scaling, normalizing, combining values). Connect float outputs from prior nodes to inputs a and b, then choose the desired operation. For sqrt and abs, only input a is used.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">First float operand. Used as the primary input for all operations and the sole input for sqrt and abs.</td><td style="word-wrap: break-word;">3.5</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Second float operand. Used for binary operations (add, subtract, multiply, divide, modulo, power). Ignored for sqrt and abs.</td><td style="word-wrap: break-word;">2.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">SELECT</td><td style="word-wrap: break-word;">Mathematical operation to apply. Options: add (a + b), subtract (a - b), multiply (a * b), divide (a / b), modulo (a % b), power (a ** b), sqrt (sqrt(a)), abs (abs(a)).</td><td style="word-wrap: break-word;">divide</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed float result for the selected operation. Returns 0.0 if an invalid operation occurs (e.g., division by zero, modulo by zero, sqrt of a negative).</td><td style="word-wrap: break-word;">1.75</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Operation scope**: sqrt and abs only use input a; input b is ignored for these.
- **Division/modulo by zero**: If b is 0 for divide or modulo, the node returns 0.0 and logs a warning.
- **Square root domain**: If a is negative for sqrt, the node returns 0.0 and logs a warning.
- **Input ranges**: a and b accept values in [-1e10, 1e10] with a step of 0.001.
- **Precision/tolerance**: Results are standard Python float computations; consider floating-point precision implications for equality or modulo.
- **Power operation**: Large exponents or certain combinations may overflow or produce infinities/NaN depending on inputs.

## Troubleshooting
- **Result is 0.0 unexpectedly**: Check if you performed divide/modulo by zero or sqrt on a negative number; the node returns 0.0 in these cases.
- **Unexpected result for sqrt/abs**: Ensure you supplied the intended value to a; b is ignored for these operations.
- **Inaccurate decimal results**: Floating-point arithmetic can introduce rounding errors. If exactness is required, consider scaling or alternative numeric handling before/after this node.
- **Overflow or inf results with power**: Reduce magnitude of a or b to avoid overflow, or clamp values upstream.
- **Modulo behavior with floats**: Floating-point modulo follows Python semantics and may be sensitive to precision; consider rounding inputs if needed.
