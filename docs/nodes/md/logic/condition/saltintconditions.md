# Int Condition

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

The Int Condition node compares two integer values using a selected comparison operator and returns a boolean result. It supports equality, inequality, and standard relational comparisons such as less-than and greater-than. This makes it a core building block for integer-based branching and control logic within Salt workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltintconditions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Int Condition node whenever you need to decide behavior based on the relationship between two integers. Common scenarios include loop termination checks (for example, index < max_iterations), triggering actions when a counter reaches or exceeds a threshold (for example, attempts >= max_attempts), or comparing two computed values. It is typically placed downstream of nodes that produce integer outputs, such as arithmetic or counter nodes (for example, SaltIntMathOperation), and upstream of logic or routing nodes that consume a BOOLEAN, such as IfConditionSelector, SaltBoolOperationNode, or any node that toggles behavior based on a boolean input. A frequent pattern is: generate a counter with SaltIntMathOperation, compare it to a constant using Int Condition, then send the resulting BOOLEAN into an IfConditionSelector to choose between two data paths. For maintainable workflows, choose the operator that most directly expresses your intent (for example, use ">=" instead of combining ">" and equality checks elsewhere) and keep your integer values within practical ranges you can reason about.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">First integer operand for the comparison. Must be a valid integer within a very wide 64-bit style range (from approximately -0xffffffffffffffff to 0xffffffffffffffff). Typically this is a dynamic value such as a loop index, iteration counter, or calculated score.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Second integer operand for the comparison. Must be a valid integer in the same wide range as input a. Often used as a fixed threshold, upper bound, or another computed integer value to compare against.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">ENUM["==","!=","<",">","<=",">="]</td><td style="word-wrap: break-word;">The comparison operator applied between a and b. Supported values: "==" (a equals b), "!=" (a does not equal b), "<" (a is less than b), ">" (a is greater than b), "<=" (a is less than or equal to b), ">=" (a is greater than or equal to b). The node evaluates this relation and outputs a boolean.</td><td style="word-wrap: break-word;"><=</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean result of applying the selected comparison operation to a and b. The value is True if the condition holds and False otherwise. This single boolean output can be wired into downstream conditional or boolean-processing nodes to control flow and decisions.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs a single primitive integer comparison and is effectively constant-time, so it can be used freely in tight loops or large graphs without noticeable performance impact.
- **Limitations**: If an unsupported or malformed operation value somehow reaches the node, it logs a warning and returns False, which may unintentionally bias your logic toward the false branch if not monitored.
- **Behavior**: If an exception occurs during evaluation (for example, due to non-integer data being forced into the inputs), the node logs an error and returns False instead of raising, allowing the workflow to continue but with a conservative false result.
- **Behavior**: The accepted integer range is extremely large; while the node can handle such values, you should ensure that upstream computations stay within ranges that are meaningful for your application and avoid unintended overflows or type mismatches.

## Troubleshooting
- **Condition always False**: If result is always False, verify that both a and b are valid integers and that the selected operation matches your intent (for example, using "<" when you actually need "<="). Also ensure no upstream node is sending strings or other types into these inputs through implicit casting.
- **Unexpected branching behavior**: When using this node to drive other logic (for example, via IfConditionSelector), confirm that the result output is correctly connected to the appropriate boolean input. A missing or miswired connection can make it seem like the condition is wrong when it is simply not being used.
- **Silent fallback to False with logs**: If you see log entries about an unknown operation or an error in the int condition, it indicates an invalid operation value or incompatible input types. Fix this by restricting the operation input to one of the allowed values ("==", "!=", "<", ">", "<=", ">=") and ensuring that only INT outputs are connected to a and b.
