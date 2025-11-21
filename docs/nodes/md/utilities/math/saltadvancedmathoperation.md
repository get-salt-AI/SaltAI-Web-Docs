# Advanced Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs a set of advanced mathematical operations on a single value, with optional use of a second integer for number-theoretic functions. Supports absolute value, square root, cube root, factorial (of the integer part), greatest common divisor (GCD), and least common multiple (LCM). Returns the computed result as a float and safeguards against invalid inputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltadvancedmathoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need single-operand advanced math or integer number theory operations within a workflow. Typical patterns include: cleaning values via abs, deriving magnitudes with sqrt/cbrt, computing factorials for integer counts, or combining an input with a second integer using gcd/lcm. For gcd and lcm, supply the second_value; for other operations it is ignored.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Primary numeric input used by all operations. Interpreted as a real number; for integer-specific operations (factorial, gcd, lcm) its integer part is used.</td><td style="word-wrap: break-word;">16.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Select the operation to perform: abs, sqrt, cbrt, factorial, gcd, or lcm. For gcd/lcm, second_value is required.</td><td style="word-wrap: break-word;">sqrt</td></tr>
<tr><td style="word-wrap: break-word;">second_value</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Secondary integer used only for gcd and lcm operations. Ignored for abs, sqrt, cbrt, and factorial.</td><td style="word-wrap: break-word;">12</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed result of the selected operation. Integer results (e.g., from gcd/lcm/factorial) are returned as floats.</td><td style="word-wrap: break-word;">4.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Operation behaviors**: abs returns |value|; sqrt returns the square root (requires value >= 0); cbrt returns the cube root; factorial uses the integer part of |value|; gcd/lcm use the integer parts of |value| and |second_value|.
- **Invalid inputs return 0.0**: The node returns 0.0 when inputs are invalid (e.g., sqrt of a negative number).
- **Factorial limits**: Factorial is computed only up to 170 to prevent overflow; larger inputs yield 0.0.
- **Integer casting for number theory**: For factorial, gcd, and lcm, both inputs are converted to non-negative integers by taking their absolute values and integer parts.
- **LCM with zeros**: If either integer is 0, lcm returns 0.
- **Cube root edge cases**: Cube root is computed via exponentiation. In some environments, negative inputs may not produce a real cube root; if an error occurs, the node returns 0.0.

## Troubleshooting
- **Result is 0.0 unexpectedly**: Check for invalid input combinations (e.g., negative value for sqrt, factorial beyond 170) or errors from cbrt on negative values.
- **gcd/lcm not as expected**: Remember values are converted to integers via absolute value and truncation; decimals are discarded.
- **second_value seemingly ignored**: second_value only applies to gcd and lcm; it is not used for abs, sqrt, cbrt, or factorial.
- **Large factorial overflow concerns**: If using values above 170, reduce the input or reconsider the approach; the node will cap by returning 0.0.
