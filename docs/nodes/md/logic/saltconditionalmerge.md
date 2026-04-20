# Merge If Branches

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltConditionalMerge collects the outputs of multiple conditional or loop branches and merges them into a single result. It scans the provided branch inputs in order and returns the first one that has a non-None value. This is typically used to re-join execution paths after If/Else logic or conditional loops so downstream nodes can work with a single unified result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltconditionalmerge.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltConditionalMerge whenever your workflow can follow multiple conditional paths but you want to continue with a single unified output. A common pattern is: evaluate a condition with nodes like Simple If (SaltIfNode) or Do While Loop (SaltIfNodeSimpleLoop), perform different operations in each branch, then route the final outputs of those branches into this merge node. The node will automatically detect which branch actually produced a value at runtime and pass that forward as merged_result. When connecting branches, wire the most preferred or most common branch to branch_1, since if more than one branch is populated the node returns the lowest-numbered non-empty input. Typical upstream nodes include conditional logic nodes in the SALT/Logic/LangGraph category, math or transformation nodes that compute branch-specific outputs, and loop constructs like Do While Loop. Typical downstream nodes include any consumer of the merged value, such as SaltOutput for exposing results, nodes that store or send the merged data, or further logic nodes that operate on whichever branch succeeded. Best practice is to ensure that only one branch is expected to execute in normal usage; if more than one can be non-empty, rely on the input ordering semantics (branch_1 over branch_2) and document that behavior in your flow for clarity.

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
<tr><td style="word-wrap: break-word;">branch_1</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional result value from the first conditional or loop branch. Can be any data type such as text, number, object, or image. If this input is not connected or evaluates to None, the node will look at subsequent branch inputs. When multiple branches have values, this branch has highest priority and will be chosen first.</td><td style="word-wrap: break-word;">Final customer summary string produced when a condition for an existing customer is true, for example: "Existing customer Jane Doe has an active premium subscription."</td></tr>
<tr><td style="word-wrap: break-word;">branch_2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional result value from the second conditional or loop branch. Also accepts any data type. Used as a fallback when branch_1 is empty or not executed. If both branch_1 and branch_2 have values, branch_1 will be returned and this value will be ignored.</td><td style="word-wrap: break-word;">Alternative customer summary string when the condition for a new customer is true, for example: "New customer registered: John Smith, trial plan activated."</td></tr>
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
<tr><td style="word-wrap: break-word;">merged_result</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The first non-None branch value among the provided branch inputs, in ascending branch order. If no branch has a value, this output is None. The data type matches whichever branch was selected, so downstream nodes must be prepared to handle that type; usually all branches are designed to produce the same kind of data.</td><td style="word-wrap: break-word;">"Existing customer Jane Doe has an active premium subscription." if branch_1 was taken, or "New customer registered: John Smith, trial plan activated." if branch_2 was taken.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs a simple scan over the branch inputs and stops at the first non-empty value, so its overhead is negligible even in large graphs.
- **Limitations**: It does not attempt to reconcile or combine multiple branch outputs; if more than one branch provides a value, only the lowest-numbered non-empty branch is used and the rest are ignored.
- **Behavior**: If all branch inputs are None or unconnected, merged_result will be None, which can cause downstream nodes to fail if they assume a valid value.
- **Behavior**: In loop scenarios with nodes like Do While Loop, the node receives the loop’s final output when you connect the loop’s result port to a branch input, rather than any initial seed value.
- **Limitations**: All branches should ideally produce compatible data types and shapes; mixing incompatible outputs, for example an image on one branch and a plain string on another, can make downstream handling brittle.

## Troubleshooting
- **Merged result is None**: Symptom: merged_result is None or downstream nodes error on missing input. Check that at least one branch is actually executed and connected, and that the producing nodes do not return None. Also verify that you wired the final output of loops or conditionals, not an initial value, into the merge node.
- **Unexpected branch selected**: Symptom: The workflow appears to use the wrong branch's output. This can happen if more than one branch produces a non-None value. Remember that the node always returns the lowest-numbered non-empty branch (for example, branch_1 over branch_2). Reorder connections or adjust conditions so that only one branch should be active at a time.
- **Type mismatch in downstream node**: Symptom: The node after SaltConditionalMerge fails with a type or format error. Ensure all branches feeding the merge node produce the same kind of data, for example all branches return a string summary or all return a JSON-like object, so that downstream nodes can safely consume merged_result.
- **Merge seems to use stale loop values**: Symptom: Merged value seems stale or equal to an initial loop value. Confirm that in loop setups you are connecting the loop's final or result output to the branch input, not an initial seed value node, and that the loop’s termination condition is configured correctly.
