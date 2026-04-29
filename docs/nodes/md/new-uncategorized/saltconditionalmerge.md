# Merge If Branches

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltConditionalMerge takes the outputs of conditional branches (commonly from Simple If or other condition nodes) and merges them into a single unified result. It examines its branch inputs in a fixed order and returns the first one that is not None. If no connected branches produced a value, it returns None so downstream nodes receive a single, consistent output regardless of which branch ran.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltconditionalmerge.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltConditionalMerge whenever your workflow splits into conditional branches that should eventually rejoin into one path. A typical pattern: an upstream logic node (such as SaltIfNode / Simple If) routes values into separate true/false or alternative branches, each performing its own processing; the final node of each branch connects into SaltConditionalMerge as `branch_1` and `branch_2`. Place this node at the convergence point where you want to continue with a single result, for example to send a final message, call an API, or store a decision object. Connect the primary or higher-priority branch to `branch_1` and the fallback or secondary branch to `branch_2`, since the node always prefers lower-numbered inputs. For best results, design all branches to output the same type and structure (e.g., a standardized decision dict or response object) so downstream nodes can treat `merged_result` uniformly. In more complex graphs, you can first consolidate multiple paths into two main outcomes before merging them here.

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
<tr><td style="word-wrap: break-word;">branch_1</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The first conditional branch result. Optional, and accepts any type (*), including text, numbers, dict-like objects, or more complex structures. This is usually wired from the primary or 'true' branch of a conditional. At runtime, if `branch_1` is not None, it becomes the merged result and the node ignores all later branches.</td><td style="word-wrap: break-word;">{"status":"approved","limit":5000,"reason":"User meets all automated checks"}</td></tr>
<tr><td style="word-wrap: break-word;">branch_2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The second conditional branch result. Optional, and accepts any type (*), just like `branch_1`. Typically wired from the alternative or 'false' branch of a conditional. It is only considered if `branch_1` is None; if `branch_1` is None and `branch_2` is non-None, `branch_2` becomes the merged result.</td><td style="word-wrap: break-word;">{"status":"manual_review","reason":"Flagged by risk model","escalation_level":2}</td></tr>
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
<tr><td style="word-wrap: break-word;">merged_result</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The unified result from the conditional branches. Its type and structure exactly match whichever branch produced the first non-None value (checked in the order `branch_1`, then `branch_2`). If all connected branches are None or unconnected, this output is None. Downstream nodes should be prepared to handle this single result and, if needed, validate that it is not None before further processing.</td><td style="word-wrap: break-word;">{"status":"approved","limit":5000,"reason":"User meets all automated checks"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node simply iterates over its provided inputs and returns the first non-None value, so runtime and resource usage are minimal even in large workflows.
- **Limitations**: Only two branches (`branch_1` and `branch_2`) are supported directly. To merge more than two paths, first combine them upstream so that at most two consolidated results feed into this node.
- **Behavior**: If more than one branch produces a non-None value, the lower-numbered input wins: `branch_1` takes precedence over `branch_2`. This priority may hide values from later branches, so connect higher-priority branches to lower-numbered inputs.
- **Behavior**: If no branch produces a value (all are None), `merged_result` will be None. Any downstream logic that requires a valid result should include checks or a fallback path.
- **Behavior**: The node does not transform or validate data types; it forwards the chosen branch value as-is. Ensure branch outputs are compatible with downstream nodes.

## Troubleshooting
- **merged_result is always from the same branch when I expect the other**: Verify which upstream outputs are connected to `branch_1` versus `branch_2`. Because `branch_1` has higher priority, if both branches can produce values, you may need to swap connections or adjust logic so only one branch can emit a non-None result for a given run.
- **merged_result is None even though a branch ran**: Check that the branch’s final node actually outputs a non-None value and that this final output is connected directly to `branch_1` or `branch_2`. Inspect for intermediate nodes that might return None or for missing connections.
- **Downstream node errors due to unexpected structure**: Ensure that all branches feeding into SaltConditionalMerge output compatible data formats (for example, always a dict with the same keys). If one branch outputs a string and another a dict, normalize these results (e.g., map both to a standard dict) before merging.
- **Hard to tell which branch was chosen**: Temporarily add logging or inspection nodes just before SaltConditionalMerge on each branch, or review execution logs that indicate which branch input (`branch_1` or `branch_2`) provided the non-None value. This helps confirm that your branching logic behaves as intended.
