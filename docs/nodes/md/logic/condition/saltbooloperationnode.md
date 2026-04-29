# Bool Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node evaluates simple boolean logic between two boolean values. It supports AND, OR, XOR, and a unary NOT on the first input, always returning a single BOOLEAN output. It is useful for combining multiple conditions or toggling logic branches in larger workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltbooloperationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Bool Operation node whenever you need to combine or invert boolean conditions to drive control flow in your Salt graphs. Typical upstream nodes include condition evaluators such as Int Condition (SaltIntConditions), Float Condition (SaltFloatConditions), String Condition (SaltStringConditions), and To Bool (SaltToBoolNode), which all produce BOOLEAN outputs. The Bool Operation node then merges these booleans (for example, requiring multiple conditions to be true with AND, or allowing any of them to be true with OR) before passing the resulting BOOLEAN into downstream logic controllers like If (IfConditionSelector) or other routing/branching nodes. Common patterns include: (1) chaining multiple condition checks and aggregating them with AND/OR, (2) isolating exclusive conditions with XOR, and (3) inverting a single flag using 'NOT a' to simplify branch expressions. For clarity and maintainability, keep the logic here simple (one operation per node) and use multiple Bool Operation nodes if you need more complex expressions.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">First boolean operand for the logical expression. Used directly in all operations and is the value negated when using the 'NOT a' option.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Second boolean operand, used in all binary operations (AND, OR, XOR). Ignored when the operation is 'NOT a'.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">op</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Logical operation to apply. Must be one of: 'a AND b', 'a OR b', 'a XOR b', 'NOT a'. For 'NOT a', the value of b is not used.</td><td style="word-wrap: break-word;">a AND b</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">The boolean result of the selected logical operation over inputs a and b. This is typically fed into other logic/flow-control nodes to enable or disable branches.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs only simple boolean operations and is effectively instantaneous; performance impact is negligible even when used many times in a graph.
- **Limitations**: The 'NOT a' operation ignores the value of b entirely; if you need to negate or combine b, wire it as the first input in another Bool Operation node.
- **Limitations**: Only single-step operations are supported (no chained expressions like '(a AND b) OR c' in one node); use multiple Bool Operation nodes to construct more complex logic.
- **Behavior**: If an unsupported operation string is somehow provided, the node logs a warning and returns False, which can cause branches to be skipped if not anticipated.

## Troubleshooting
- **Unexpected False result**: If you always get False, verify that 'op' is set to one of the supported values ('a AND b', 'a OR b', 'a XOR b', 'NOT a'). An invalid value will default the result to False.
- **b seems ignored**: When using the 'NOT a' option, input b is intentionally not used. If you meant to combine a and b, select 'a AND b', 'a OR b', or 'a XOR b' instead.
- **Logic appears inverted**: If downstream behavior seems opposite to what you expect, double-check the input wiring (which condition is 'a' vs 'b') and whether you accidentally used 'NOT a' where a direct operation was intended.
