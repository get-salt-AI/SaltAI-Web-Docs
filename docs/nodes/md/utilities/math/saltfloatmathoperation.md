# Float Math Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node applies a selected mathematical operation to one or two floating-point inputs. It supports arithmetic (add, subtract, multiply, divide, modulo, power) as well as unary operations (square root, absolute value) on float values. It is useful for numeric control, scaling, and formula-based adjustments inside Salt workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltfloatmathoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to manipulate float values in a pipeline, such as scaling model parameters, normalizing scores, adjusting thresholds, or combining numeric outputs from previous nodes. Typically it sits mid-pipeline, receiving floats from nodes like settings or analysis nodes, and feeding its result into downstream logic, control, or routing nodes. Pair it upstream with nodes that produce FLOAT values (for example, configuration or metrics nodes) and downstream with nodes that require a computed FLOAT (such as condition/comparison nodes, weighting or blending nodes, or scheduler/loop controllers). Use binary operations (add, subtract, multiply, divide, modulo, power) when combining two floats, and unary operations (sqrt, abs) when transforming a single value; for unary operations, the node still requires both inputs but will effectively only use the first operand in the computation. Prefer this node over the integer variant when precision and fractional values are important.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">First float operand and the primary value used in all operations. For binary operations (add, subtract, multiply, divide, modulo, power), it is combined with b. For unary operations (sqrt, abs), this is the value the function is applied to. Accepts values from -1e10 to 1e10 with millisecond (0.001) resolution.</td><td style="word-wrap: break-word;">0.75</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Second float operand used for binary operations (add, subtract, multiply, divide, modulo, power). For unary operations like sqrt and abs, this input is still required by the interface but is typically ignored by the calculation. Accepts values from -1e10 to 1e10 with millisecond (0.001) resolution.</td><td style="word-wrap: break-word;">1.25</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Selects which mathematical operation to perform on the inputs. Supported values: "add" (a + b), "subtract" (a - b), "multiply" (a * b), "divide" (a / b), "modulo" (a % b), "power" (a ** b), "sqrt" (square root of a), "abs" (absolute value of a). Some operations impose natural constraints, such as division by zero or square root of negative numbers, which may cause errors or fallback behavior depending on engine handling.</td><td style="word-wrap: break-word;">multiply</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The float result of applying the selected operation to the inputs. This is a single scalar float value that can be fed into downstream nodes for further numeric processing, threshold checks, weighting, or configuration control.</td><td style="word-wrap: break-word;">2.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Operations are constant-time and lightweight, so this node can be used freely even inside larger or repeated workflows without noticeable overhead.
- **Limitations**: Division or modulo by zero and invalid domains for functions like sqrt on negative numbers can raise errors or produce undefined results; ensure your inputs are validated or constrained upstream.
- **Behavior**: For unary operations such as "sqrt" and "abs", the node still requires both inputs a and b, but only a is meaningfully used in the computation.
- **Behavior**: Input ranges are clamped between -1e10 and 1e10; values outside this range may be rejected or clipped by the UI or engine configuration.
- **Integration**: For integer-only logic, prefer the "SaltIntMathOperation" node to avoid implicit casting and to reflect your intent more clearly.

## Troubleshooting
- **Common Error 1**: "division by zero" or similar error when using the "divide" or "modulo" operation. - Fix: Ensure input b is never 0.0 by clamping it upstream, adding a minimum epsilon (e.g., 1e-6), or branching your workflow to skip division when b is zero.
- **Common Error 2**: Unexpected results or errors when using "sqrt" with negative values for a. - Fix: Validate that a is non-negative before applying "sqrt", or insert an "abs" operation upstream to make values positive.
- **Common Error 3**: Result seems truncated or does not match very large/small expectations. - Fix: Check that your inputs are within the -1e10 to 1e10 range and that earlier nodes are not clipping or rounding your values.
- **Common Error 4**: Operation appears to ignore b when using "sqrt" or "abs". - Fix: This is expected; those operations are unary and apply only to a. If you intended to combine both values, choose a binary operation such as "add" or "multiply" instead.
