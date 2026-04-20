# Float Condition

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node compares two float values using a chosen relational operator (==, !=, <, >, <=, >=) and outputs the boolean result. It is designed for simple numeric decision logic, such as threshold checks or ordering comparisons between continuous values. As part of the SALT/Logic/Conditions family, it focuses on clear, robust float comparisons without side effects.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltfloatconditions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to make a decision based on a floating-point value, for example deciding if a model confidence is above a threshold, if a progress percentage has reached completion, or if one numeric metric is larger than another. It typically sits in the middle of a workflow: upstream nodes supply numeric FLOAT values (e.g., numeric parameter nodes, scoring or measurement nodes), and this node converts their relationship into a BOOLEAN. Downstream, the `result` output is commonly fed into `SaltBoolOperationNode` to combine multiple conditions, or into branching/control nodes like `IfConditionSelector` to route different data or execution paths depending on True/False. A common pattern is: numeric source → SaltFloatConditions → IfConditionSelector to choose between two alternative inputs or flows based on the comparison. Prefer this node over embedding custom comparison logic elsewhere when you want your conditions to be explicit, easy to read, and reusable.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Left-hand operand of the comparison. A floating-point number between -999999999999.0 and 999999999999.0. It is compared to `b` using the selected `operation` (for example, a > b).</td><td style="word-wrap: break-word;">0.82</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Right-hand operand of the comparison. A floating-point number between -999999999999.0 and 999999999999.0. It is the value against which `a` is compared (for example, a > b).</td><td style="word-wrap: break-word;">0.75</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">["==", "!=", "<", ">", "<=", ">="]</td><td style="word-wrap: break-word;">The relational operator that defines how `a` and `b` are compared. Only the six listed operators are valid: equality, inequality, and ordering checks. If any other value is somehow provided, the node logs a warning internally and returns False.</td><td style="word-wrap: break-word;">></td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">The boolean outcome of applying the selected `operation` to `a` and `b`. It is True if the condition holds (for example, a > b is satisfied), and False otherwise. This output is typically consumed by other logic or flow-control nodes to drive branching or gating behavior.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs a single primitive numeric comparison and is effectively constant-time with negligible resource usage, making it safe to use heavily in complex logic graphs.
- **Limitations**: Only the basic operators ==, !=, <, >, <=, and >= are supported; complex conditions (like ranges or multi-step logic) must be built using multiple nodes and, if needed, combined with SaltBoolOperationNode.
- **Limitations**: Inputs must lie within the configured bounds (from -999999999999.0 to 999999999999.0). Extremely large magnitudes, NaN, or infinite values can lead to unexpected comparison behavior or may not be representable by the UI constraints.
- **Behavior**: If an unsupported operation string is provided, the node logs a warning and returns False instead of failing, so misconfigured operations can silently appear as consistently False conditions if logs are not monitored.
- **Behavior**: Any runtime error during evaluation (for example, caused by malformed or incompatible upstream data) is caught, and the node returns False, so downstream logic should treat a False result as "condition not met or evaluation failed" rather than assuming strictly logical failure.

## Troubleshooting
- **Condition always evaluates to False**: Confirm that `operation` is one of `==`, `!=`, `<`, `>`, `<=`, or `>=`, and check that both `a` and `b` are valid FLOAT values in the allowed range. An invalid operator or an upstream type/format issue will cause the node to default to False.
- **Unexpected equality results with decimal values**: Floating-point precision can cause two numbers that look the same in the interface to differ slightly in memory, making `==` comparisons fail. When implementing thresholds or tolerance-based checks, prefer `>=` or `<=`, or round values upstream before comparison.
- **Branching node ignores the result**: If a downstream branching node (such as IfConditionSelector) seems to ignore this node’s output, verify that its boolean input is wired to `result` and that no other condition or override is short-circuiting the logic. Also ensure `result` is not being overwritten by another node later in the graph.
- **Errors or warnings in logs but no visible failure**: The node is designed to catch exceptions and return False on error. If you see unexpected False results, inspect runtime logs for warnings about unknown operations or evaluation errors, correct the configuration (particularly the `operation` value and upstream numeric sources), and re-run the workflow.
