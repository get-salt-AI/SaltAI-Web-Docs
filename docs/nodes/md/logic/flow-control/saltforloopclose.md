# For Loop Close (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltForLoopClose is the closing node for Salt’s deprecated for-loop flow-control construct. It transforms for-loop semantics into a while-based loop by using integer arithmetic and a comparison condition, then delegates execution to SaltWhileLoopClose. When the loop completes, it outputs the final values of all loop-carried variables; if an internal error occurs, it returns placeholder None values instead of raising an error.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltforloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltForLoopClose only in existing or legacy graphs that already rely on the deprecated For Loop Open / For Loop Close pattern; for new workflows, prefer the newer Loop Open / Loop Close nodes. Place SaltForLoopClose at the end of the loop body that starts with SaltForLoopOpen. The flow_control input must be wired directly from SaltForLoopOpen so this node can attach to the same loop context. Any per-iteration state you want to persist across the loop should be connected as initial_valueN inputs; the loop body updates these across iterations, and the matching valueN outputs then expose the final states to downstream nodes.

A common pattern is: (1) SaltForLoopOpen defines the iteration count or range and produces a flow_control handle; (2) one or more processing nodes implement the per-iteration logic; (3) SaltForLoopClose uses that flow_control handle plus any initial_valueN inputs to construct an internal subgraph with SaltIntMathOperation, SaltIntConditions, and SaltWhileLoopClose to perform the repeated execution. After the loop is finished, downstream nodes can consume the valueN outputs to build responses, compute aggregates, or drive subsequent branching. Keep loop-carried types consistent, and avoid mixing this deprecated pair with the newer Loop Open / Loop Close nodes on the same control path to reduce confusion.

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
<tr><td style="word-wrap: break-word;">flow_control</td><td>True</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">Loop control handle that ties this node to its corresponding For Loop Open and the underlying while-loop execution. It must be the flow_control output from SaltForLoopOpen (or a compatible loop open node) and is passed as a raw link. Without a valid flow_control, the node cannot construct the internal loop and will fall back to returning None outputs.</td><td style="word-wrap: break-word;">The flow_control output from a SaltForLoopOpen node configured to iterate from 0 to 9 (10 iterations).</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First optional loop-carried value. This is an initial state fed into the loop body on the first iteration and updated each time through the loop, with the final result exposed on value1. It can be any supported data type, but must remain consistent with the logic in the loop body.</td><td style="word-wrap: break-word;">An integer 0 used as an accumulator that adds the current index each iteration to compute a total sum.</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second optional loop-carried variable. Use this when you need a separate piece of evolving state, such as a list of collected items or a secondary numeric counter. Its final value will be produced on value2.</td><td style="word-wrap: break-word;">An empty list [] that will collect an order ID string from each iteration of the loop.</td></tr>
<tr><td style="word-wrap: break-word;">initial_value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third optional loop-carried state slot. If connected, it receives an initial value before the first iteration and is updated by the loop body; the final result is available through value3. If left unconnected, the corresponding output is typically None.</td><td style="word-wrap: break-word;">A string "" that is appended with status messages like "step i OK" from each loop iteration.</td></tr>
<tr><td style="word-wrap: break-word;">initial_value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fourth optional loop-carried variable. The node defines multiple such inputs (up to an internal maximum) as generic wildcards, and you only need to connect those you actively use in the loop body.</td><td style="word-wrap: break-word;">A JSON object {"min_score": 1.0, "max_score": 0.0} that is updated when lower or higher scores are seen during each iteration.</td></tr>
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
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the first loop-carried variable that originated from initial_value1, after all iterations have completed. If initial_value1 was not connected or the internal loop failed, this will be None.</td><td style="word-wrap: break-word;">An integer 55 representing the sum of indices from 0 to 10 accumulated across the loop.</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the second loop-carried variable corresponding to initial_value2. Often used for lists or other aggregates built across iterations.</td><td style="word-wrap: break-word;">A list of user IDs ["user-01", "user-02", "user-03"] collected over three iterations.</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the third loop-carried variable corresponding to initial_value3. If the corresponding input was left unconnected or invalid, this may be None and should then be ignored by downstream nodes.</td><td style="word-wrap: break-word;">A string "Job 0 OK; Job 1 OK; Job 2 OK" containing concatenated per-iteration status messages.</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the fourth loop-carried variable corresponding to initial_value4. Additional valueN outputs beyond this (if present in your environment) follow the same pattern: each reflects the final state of its matching initial_valueN input.</td><td style="word-wrap: break-word;">A JSON object {"min_score": 0.72, "max_score": 0.98} summarizing score statistics collected during the loop.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node expands an internal loop graph using integer math, a comparison node, and a while-loop close node; high iteration counts or heavy loop bodies can significantly increase execution time and memory usage.
- **Limitations**: This for-loop close node is explicitly marked as deprecated; it may not receive new features and could be removed in future versions. Prefer Loop Open and Loop Close for new designs.
- **Behavior**: On any internal exception (such as invalid flow_control, graph construction failure, or runtime errors inside the loop body), the node logs an error and returns a tuple of None values and an empty expansion instead of raising a hard error through the pipeline.
- **Behavior**: All initial_valueN inputs and valueN outputs are wildcard raw links without type enforcement. You must ensure that each loop-carried variable keeps a consistent data type across all iterations and branches to avoid subtle failures in the loop body or downstream.

## Troubleshooting
- **Common Error 1**: All valueN outputs are None even though inputs are wired. This usually indicates that flow_control is missing, not taken from SaltForLoopOpen, or an internal error occurred. Verify that flow_control comes directly from SaltForLoopOpen and that the loop body does not contain operations that could raise errors (for example, invalid arithmetic or null dereferences).
- **Common Error 2**: The loop runs zero or an unexpected number of times. Since the for-loop is implemented via integer math and a 'greater-than' condition internally, incorrect configuration on SaltForLoopOpen (for example, start/end values or step) can cause off-by-one or no-iteration behavior. Recheck the iteration range and ensure the counter and termination condition align with your intent.
- **Common Error 3**: Downstream nodes fail with type or structure errors immediately after the loop. This typically happens when different iterations or conditional branches in the loop body produce different types for the same loop-carried variable (e.g., sometimes a list, sometimes a string). Enforce consistent types and shapes for every loop-carried variable on all execution paths inside the loop.
