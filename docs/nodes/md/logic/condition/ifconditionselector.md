# If

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates a condition between inputs A and B and routes the output based on the result. It can either forward one of two provided inputs (TRUE_IN/FALSE_IN) or output a boolean, and supports lazy evaluation so only inputs required by the selected condition are executed.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/ifconditionselector.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to branch workflow execution based on comparisons, membership tests, bitwise/boolean operations, or a custom expression. Enable Return Inputs (require_inputs) to forward either TRUE_IN or FALSE_IN when the condition is true/false; disable it to simply output a boolean result for downstream logic.

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
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The condition to evaluate between A and B. Options: A is TRUE, B is TRUE, A is NONE, B is NONE, A == B, A != B, A > B, A >= B, A < B, A <= B, A is B, A is not B, A in B, B in A, A & B, A \| B, A ^ B, CUSTOM.</td><td style="word-wrap: break-word;">A == B</td></tr>
<tr><td style="word-wrap: break-word;">require_inputs</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When enabled, the node forwards TRUE_IN if the condition is true, otherwise FALSE_IN. When disabled, the node outputs a boolean result instead.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">NOT</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Invert the result after evaluation. For boolean results, logical NOT is applied; for non-boolean results from bitwise ops, bitwise inversion is applied.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">custom_expression</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Expression to evaluate when condition is CUSTOM. You can reference A/a and B/b and any named global variables from Memory Storage nodes. Most standard Python expressions, types, and built-ins supported by the node’s safe evaluator are allowed.</td><td style="word-wrap: break-word;">len(a) > b</td></tr>
<tr><td style="word-wrap: break-word;">A</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">First input to evaluate. Only required for conditions that reference A (including CUSTOM expressions that use a/A).</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">B</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Second input to evaluate. Only required for conditions that reference B (including CUSTOM expressions that use b/B).</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">TRUE_IN</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Value to forward if the evaluated condition is true. Required when require_inputs is enabled and the condition can be evaluated.</td><td style="word-wrap: break-word;">Route to branch A</td></tr>
<tr><td style="word-wrap: break-word;">FALSE_IN</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Value to forward if the evaluated condition is false. Required when require_inputs is enabled and the condition can be evaluated.</td><td style="word-wrap: break-word;">Route to branch B</td></tr>
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
<tr><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">If require_inputs is enabled, forwards TRUE_IN when the condition is true, otherwise FALSE_IN. If disabled, outputs a boolean representing the result.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Lazy evaluation**: Only inputs referenced by the selected condition are executed (e.g., a condition that only needs A will not trigger B). With require_inputs enabled, after evaluation only the selected branch input (TRUE_IN or FALSE_IN) is executed/forwarded.
- **Custom expression**: When using CUSTOM, ensure A and B are provided if your expression references a/A or b/B. You can also use named globals from Memory Storage nodes in the expression.
- **Type compatibility**: Choose a condition that matches the types of A and B (e.g., use equality for tensors via A == B, membership when B is a container, bitwise ops for booleans/integers).
- **Equality for tensors**: A == B and A != B use specialized comparisons that are compatible with tensor-like data.
- **Inversion behavior**: NOT applies logical not for boolean results and bitwise invert (~) for non-boolean results (e.g., bitwise ops).
- **Return mode**: Set require_inputs to True to forward branch values, or False to produce a boolean for downstream logic.

## Troubleshooting
- **CUSTOM expression does not run**: If A or B is None while your expression references a or b, the node may skip computation. Provide the required inputs or adjust the expression.
- **Type error during comparison**: Ensure A and B support the chosen operation (e.g., do not use 'A in B' when B is not iterable; avoid ordering comparisons for non-orderable types).
- **Unexpected inverted result**: Check if NOT is enabled. For non-boolean results (bitwise ops), inversion uses ~ which differs from logical not.
- **Nothing forwarded when expecting branch output**: Confirm require_inputs is enabled and both TRUE_IN and FALSE_IN are connected. If disabled, the node outputs a boolean instead of forwarding inputs.
- **Membership checks fail**: For 'A in B' or 'B in A', ensure the right-hand operand is a valid container and that the left-hand item has appropriate equality semantics.
- **Equality on complex data**: When comparing complex structures or tensors, prefer A == B/A != B options provided, which handle tensor-like comparisons more reliably.
