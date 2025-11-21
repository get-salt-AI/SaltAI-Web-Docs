# Advanced Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs several advanced scalar math operations on a single input and, where relevant, a second integer. Supported operations include absolute value, square root, cube root, factorial (of the integer part), greatest common divisor (GCD), and least common multiple (LCM). Results are returned as a float with guardrails for invalid domains and extreme values.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltadvancedmathoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need higher-level math beyond basic arithmetic, such as taking square roots, computing factorials, or deriving GCD/LCM with another integer. It commonly follows value-producing nodes and can feed results into control logic, numeric conditioning, or further calculations.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Primary numeric input. Used by all operations. For factorial/GCD/LCM, the integer part of \|value\| is used.</td><td style="word-wrap: break-word;">16.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select the operation to perform. Options: abs, sqrt, cbrt, factorial, gcd, lcm.</td><td style="word-wrap: break-word;">sqrt</td></tr>
<tr><td style="word-wrap: break-word;">second_value</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Secondary integer used only for GCD and LCM calculations. Ignored for other operations.</td><td style="word-wrap: break-word;">12</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed result of the selected operation. For operations that inherently produce integers (factorial, GCD, LCM), the result is returned as a float.</td><td style="word-wrap: break-word;">4.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Operation behaviors**: abs returns |value|; sqrt requires value >= 0; cbrt computes value^(1/3); factorial uses the integer part of |value|; gcd/lcm use the integer parts of |value| and |second_value|.
- **Domain limits**: sqrt of negative inputs is invalid and returns 0.0.
- **Cube root of negatives**: Negative inputs for cbrt may not be supported and can yield 0.0; use a non-negative input or handle sign externally.
- **Factorial bounds**: Factorial is limited to integers up to 170 to avoid overflow; larger inputs will return 0.0.
- **Integer casting**: For factorial, gcd, and lcm, fractional parts are discarded (casting the absolute value to an integer).
- **Second value usage**: second_value only affects gcd and lcm; it is ignored for abs, sqrt, cbrt, and factorial.

## Troubleshooting
- **Result is 0.0 unexpectedly**: Check if the operation was applied outside its valid domain (e.g., sqrt of a negative number, cbrt of a negative number) or if factorial exceeded the supported range.
- **Unexpected gcd/lcm result**: Remember both numbers are converted to their integer parts (after abs). Ensure second_value is set and is within the allowed range.
- **Factorial not matching expectation**: Only the integer part of |value| is used. If you intended a specific integer, provide that integer exactly.
- **LCM returns 0**: If either integer is 0, LCM is defined here as 0; provide non-zero integers for a non-zero LCM.
