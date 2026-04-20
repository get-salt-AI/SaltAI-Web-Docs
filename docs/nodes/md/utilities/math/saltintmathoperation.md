# Int Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node executes a selected arithmetic operation on two integer inputs and returns a single integer result. It supports addition, subtraction, multiplication, integer division, modulo, and exponentiation, with built-in guards for division or modulo by zero and unknown operations. When invalid operations occur, it logs a warning or error and safely returns 0 instead of failing the workflow.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltintmathoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need simple integer math inside a Salt workflow, such as counters, loop indices, pagination calculations, score adjustments, or branching logic based on numeric values. It typically appears early or mid-pipeline to derive numeric values that will feed into condition or evaluation nodes, control-flow nodes, or output formatting nodes. Common upstream nodes include integer sources like generic input or configuration nodes that emit INTs, or results from other utility or math nodes (for example another SaltIntMathOperation in a chain, or outputs from loop controllers). Downstream, its result output often feeds into comparison or condition nodes (such as SaltIntConditions or SaltComparisonOperation), loop controllers (like SaltIfNodeSimpleLoop), or directly into SaltOutput for reporting in structured responses. Integration patterns include loop counters (using operation "add" to increment an index), range and offset calculations for batched API calls, modular arithmetic with "modulo" for periodic actions, and integer-safe exponentiation with "power". For best results, keep operands within sensible bounds, especially when using "power", and add explicit checks upstream if division or modulo by zero has to be handled differently from the node's default behavior.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">First integer operand for the arithmetic operation. Serves as the left-hand side in expressions (a + b, a - b, a * b, a // b, a % b, a ** b). Must be within -9223372036854775808 to 9223372036854775807 (derived from -0xffffffffffffffff to 0xffffffffffffffff).</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Second integer operand, used as the right-hand side in the selected operation. Also constrained to -9223372036854775808 to 9223372036854775807. For divide and modulo, if this value is 0, the node logs a warning and returns 0 instead of raising an error.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The arithmetic operation to perform on a and b. Supported values: "add" (a + b), "subtract" (a - b), "multiply" (a * b), "divide" (integer division a // b), "modulo" (a % b), and "power" (a ** b). Any other value is treated as unknown and produces a result of 0 with a warning.</td><td style="word-wrap: break-word;">multiply</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The integer result of applying the chosen operation to inputs a and b. For valid operations, this is the computed arithmetic outcome; for guarded or invalid cases (division or modulo by zero, unknown operation, internal error), the node returns 0 to keep the workflow running.</td><td style="word-wrap: break-word;">For a=10, b=3, operation="divide", the output is 3.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Operations are constant-time integer arithmetic, but very large operands, especially with the "power" operation, can generate huge numbers and increase memory or processing time.
- **Limitations**: Division uses integer division (a // b); fractional parts are discarded. Use a float-based math node if you need decimal precision.
- **Behavior**: If b is 0 for "divide" or "modulo", the node logs a warning and returns 0 rather than raising an error, which may hide certain issues if you rely on exceptions.
- **Behavior**: If the operation input is not one of the supported strings, the node logs a warning about the unknown operation and returns 0 instead of stopping execution.

## Troubleshooting
- **Unexpected result of 0**: Check that the operation is spelled exactly as one of the supported options ("add", "subtract", "multiply", "divide", "modulo", "power"). An invalid operation string or an internal error will cause the node to return 0.
- **0 result from division or modulo**: If you receive 0 from a divide or modulo operation, ensure that b is not 0. When b is 0, the node substitutes 0 as the result and issues a warning in the logs.
- **Integer division confusion**: If you expected a decimal result (for example 10 / 3 = 3.333...), remember this node uses integer division and returns 3. Switch to a float-based math node if you require fractional results.
- **Very large outputs or slow downstream nodes**: Extremely large integer outputs, especially from "power", may cause downstream nodes to slow down or fail. Add validation or bounds checks on a and b before calling this node.
