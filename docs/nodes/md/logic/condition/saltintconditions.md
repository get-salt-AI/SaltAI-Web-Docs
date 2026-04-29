# Int Condition

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node evaluates a relational condition between two integer values using the operators ==, !=, <, >, <=, or >=. It returns a single boolean indicating whether the specified comparison is true. This makes it a basic building block for integer-based branching and control logic in Salt workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltintconditions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Int Condition node whenever you need to decide workflow behavior based on how two integers relate to each other. Common scenarios include controlling loop execution (for example, continue while a counter < max_iterations), enforcing thresholds (such as stopping when retries >= max_retries), or conditionally activating a branch (like when an index == target_index). Typically, `a` and `b` come from nodes that produce or modify integers, such as SaltIntMathOperation, loop/counter nodes, or configuration inputs. The `result` output is then wired into downstream logic or routing nodes like SaltBoolOperationNode (to combine conditions), SaltToBoolNode (when mixing with non-boolean values), or IfConditionSelector and other branching/loop nodes that route data based on a boolean. When building more complex logic, chain multiple Int Condition nodes with boolean logic nodes rather than overloading a single comparison.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Left-hand side integer operand for the comparison. Must be an integer within the configured range (from −0xffffffffffffffff to +0xffffffffffffffff). Often used as a counter, index, or measured value.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Right-hand side integer operand for the comparison. Must be an integer within the same range as `a`. Commonly used as a target value, limit, or threshold.</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">Comparison operator applied between `a` and `b`. Supported values: `==` (equal), `!=` (not equal), `<` (less than), `>` (greater than), `<=` (less than or equal), `>=` (greater than or equal). Any other value is treated as unknown and makes the node return `False`.</td><td style="word-wrap: break-word;"><=</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean outcome of the comparison `a [operation] b`. Returns `True` when the condition holds, `False` when it does not, or when the operator is invalid or an internal error occurs. Intended to be consumed by downstream logic or routing nodes to control which branch of the workflow executes.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs a single integer comparison and minimal control logic, so it runs in constant time and can be used safely inside tight loops or iterative flows.
- **Limitations**: Inputs must be integers. If upstream nodes pass incompatible types (for example, strings or complex objects), evaluation can fail internally and the node will fall back to returning `False`.
- **Behavior**: If the `operation` value is not one of the supported operators, the node logs a warning and returns `False` instead of throwing an error or stopping the workflow.
- **Behavior**: Comparison logic is wrapped in error handling; any unexpected runtime exception is logged and causes the node to return `False`, which may silently change which branch is taken if not anticipated.

## Troubleshooting
- **Condition always evaluates to False**: Verify that `operation` is exactly one of `==`, `!=`, `<`, `>`, `<=`, `>=`. Typos such as `=>` or extra spaces result in an unknown operation and a `False` outcome.
- **Unexpected False when using dynamic inputs**: Check that both `a` and `b` are true integers, not strings like "10" or floats. Invalid types can cause an internal exception, after which the node logs an error and returns `False`.
- **Loop or branch never activates**: When using the node to control loops or branches with nodes like SaltIfNodeSimpleLoop, confirm that your comparison matches the intended logic (for example, `counter < limit` vs `counter <= limit`) and that your counter or index is actually updating between iterations.
- **Inconsistent results with large numbers**: Ensure that your upstream values stay within the supported integer range (−0xffffffffffffffff to +0xffffffffffffffff). Out-of-range or malformed numeric values can trigger errors and force the node to return `False`.
