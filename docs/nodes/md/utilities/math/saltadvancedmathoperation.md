# Advanced Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node applies a selected advanced mathematical operation to a single numeric value, optionally using a second integer for discrete operations. It supports absolute value, square root, cube root, factorial, greatest common divisor (GCD), and least common multiple (LCM). The result is always returned as a float, making it easy to plug into numeric control flows, parameter calculations, or further math nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltadvancedmathoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need higher-level math operations that go beyond simple addition or multiplication, especially for preparing or transforming numeric parameters that drive other parts of your workflow (for example, sizing, thresholds, or scaling factors). Typical scenarios include: (1) normalizing or constraining values using abs to convert possibly negative metrics into magnitudes; (2) non-linear scaling with sqrt or cbrt to compress large ranges like squared distances or error energies; (3) discrete combinatorics and counts with factorial when deriving weights or counts from integer-like parameters; (4) integer relationships using gcd and lcm to derive step sizes, tiling patterns, or synchronized intervals. In a typical pipeline, this node consumes numeric outputs from nodes such as SaltFloatMathOperation, counters, or configuration loaders and feeds its result into downstream nodes like SaltComparisonOperation, SaltRoundingOperation, or any parameter input expecting a FLOAT. Combine it with SaltLogarithmicOperation to shape numeric control curves, or with SaltIntMathOperation when you need both basic and advanced operations in sequence. Prefer this node whenever you need a single, configurable block for advanced math rather than wiring multiple specialized nodes.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Primary numeric input on which the selected advanced operation is performed. For continuous operations (abs, sqrt, cbrt) it may be any float within the allowed range; for discrete operations (factorial, gcd, lcm) it is converted to a non-negative integer via abs and int(). Valid range is -1e10 to 1e10.</td><td style="word-wrap: break-word;">36.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">Selects which advanced operation to execute. Supported values: "abs" (absolute value), "sqrt" (square root, requires value >= 0), "cbrt" (cube root, works on negative and positive values), "factorial" (factorial of the integer part of \|value\|, with a safety cap), "gcd" (greatest common divisor of integer parts of \|value\| and \|second_value\|), "lcm" (least common multiple of those integer parts).</td><td style="word-wrap: break-word;">"sqrt"</td></tr>
<tr><td style="word-wrap: break-word;">second_value</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Secondary integer input used only for "gcd" and "lcm". It is converted internally to a non-negative integer via abs(). For other operations (abs, sqrt, cbrt, factorial) it is ignored. Valid range is 1 to 1000.</td><td style="word-wrap: break-word;">24</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The numeric result of the chosen advanced operation, always returned as a float. For abs, sqrt, and cbrt it will be a standard floating-point result; for factorial, gcd, and lcm an integer result is computed and then cast to float. This output can be fed into other math, comparison, or configuration nodes that accept a FLOAT.</td><td style="word-wrap: break-word;">6.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Factorial grows extremely fast; the node caps the factorial input at an integer value of 170 to prevent overflow. Larger values will trigger a warning and return 0.0.
- **Limitations**: The "sqrt" operation on a negative "value" is not allowed; the node logs a warning and returns 0.0 for such inputs instead of producing NaN or a complex value.
- **Behavior**: For "factorial", "gcd", and "lcm", the node uses the absolute integer part of the inputs (int(abs(value)) and int(abs(second_value))), so fractional parts are discarded and negative signs are ignored.
- **Behavior**: For "lcm", if either derived integer is 0, the result is 0. This is intentional and useful to signal degenerate or invalid combinations in downstream logic.
- **Behavior**: On any unexpected internal error, the node logs the issue and returns 0.0, so pipelines keep running but should not rely on that fallback as a valid numeric result.

## Troubleshooting
- **Unexpected 0.0 result when using factorial**: If you pass a large "value" (for example, 1000), the integer part may exceed the internal safety threshold (170) and the node returns 0.0 with a warning. Reduce the magnitude of "value" or pre-scale it before this node.
- **Getting 0.0 from sqrt with negative inputs**: The node does not support square roots of negative numbers; it logs a warning and returns 0.0. Ensure "value" is >= 0 before applying "sqrt", or use "abs" first if you only care about magnitude.
- **GCD/LCM results seem off with fractional inputs**: These operations internally cast inputs to int(abs(...)). For example, 10.9 and 6.2 are treated as 10 and 6. If you need exact integer behavior, round or floor values explicitly upstream.
- **Second value appears to have no effect**: The "second_value" input is only used for "gcd" and "lcm". For "abs", "sqrt", "cbrt", and "factorial", changing "second_value" will not change the result.
