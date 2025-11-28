# Advanced Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs a set of advanced mathematical operations on a number. Operations include absolute value, square root, cube root, factorial (of the integer part), and integer greatest common divisor (GCD) and least common multiple (LCM) with a second value. Returns a single floating-point result with sensible safeguards for invalid inputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltadvancedmathoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need single-value advanced math operations within a pipeline, such as normalizing values (abs), computing roots, applying factorials for combinatorial logic, or working with integer relationships via GCD/LCM. Feed a numeric value and select the desired operation; provide the second_value only for GCD/LCM.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Primary numeric input. Used by all operations. Interpreted as a real number; for factorial, GCD, and LCM the integer part of its absolute value is used internally.</td><td style="word-wrap: break-word;">9.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Operation to perform. One of: abs, sqrt, cbrt, factorial, gcd, lcm.</td><td style="word-wrap: break-word;">sqrt</td></tr>
<tr><td style="word-wrap: break-word;">second_value</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Auxiliary integer used only for GCD and LCM. Ignored for other operations. Must be between 1 and 1000 inclusive.</td><td style="word-wrap: break-word;">12</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The result of the selected advanced operation.</td><td style="word-wrap: break-word;">3.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Factorial uses the integer part of abs(value). Very large factorials are capped for safety; extremely large inputs will return a safe default.
- Square root on negative inputs is invalid and returns a safe default result.
- Cube root is computed as a real-valued root; unusual inputs may be clamped to a safe default if unsupported.
- GCD/LCM treat both inputs as non-negative integers (using the integer part). For LCM, if either integer is 0, the result is 0.
- second_value is only relevant for gcd and lcm; it is ignored for abs, sqrt, cbrt, and factorial.

## Troubleshooting
- Result is 0.0 unexpectedly: Check if the operation has invalid inputs (e.g., sqrt of a negative number) or if the operation name is not one of the supported options.
- LCM/GCD seem off: Remember both numbers are coerced to non-negative integers using their integer parts; verify 'value' and 'second_value' after rounding/truncation.
- Factorial returns 0.0 or not as expected: Ensure 'value' is within a reasonable integer magnitude; factorial uses int(abs(value)) and extremely large values are limited for safety.
- No effect from second_value: second_value only applies to gcd and lcm operations; it is ignored for others.
