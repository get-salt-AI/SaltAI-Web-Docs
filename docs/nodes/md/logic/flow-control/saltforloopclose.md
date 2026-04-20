# For Loop Close (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltForLoopClose implements a classic counting for-loop on top of the lower-level while-loop mechanism. It consumes a flow control handle from a matching loop-open node, automatically manages the loop counter, and outputs the final values from the last completed iteration. This node is marked deprecated in favor of the newer Loop Open/Loop Close pair, but remains useful for legacy flows that were built around explicit for-loops.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltforloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node as the closing counterpart to a for-style loop when you already have a loop body driven by SaltWhileLoopOpen / SaltWhileLoopClose, or in legacy flows where a for-loop abstraction is required. It is typically placed downstream of a loop-open node (SaltForLoopOpen or a compatible loop initializer) and directly after the loop body logic that operates once per iteration. Upstream, you pass the flow_control output created by the loop-open node (which encapsulates the current iteration state and max count) and any values you want to carry through the loop as initial_value sockets. Downstream, you connect the value1, value2, … outputs of SaltForLoopClose into subsequent logic that only needs the final loop results (for example, an accumulated list of items, a final counter, or the last processed object). In modern pipelines, prefer using Loop Open and Loop Close for new implementations, but SaltForLoopClose is still important when maintaining or extending older graphs that already depend on its behavior. It integrates naturally with other logic nodes like SaltIntMathOperation and SaltIntConditions, and with typical data-processing nodes in the loop body.

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
<tr><td style="word-wrap: break-word;">flow_control</td><td>True</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">The loop flow handle coming from the corresponding loop open node. It encodes the loop counter, bounds, and internal control state needed to drive each iteration. It must be passed through unmodified from the upstream SaltForLoopOpen or compatible loop initializer.</td><td style="word-wrap: break-word;">The direct flow_control output from a SaltForLoopOpen node that was configured to iterate from 0 to 9 (10 iterations total).</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First carried-through value for the loop, forwarded into and out of the underlying while-loop. Can be any data type (text, numbers, images, JSON, etc.). Typically used for accumulators or running state across iterations.</td><td style="word-wrap: break-word;">A running list of processed product IDs that is extended in each iteration of the loop body.</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second carried-through value for the loop body. Optional and type-agnostic. Use when you need to track a second piece of state across iterations, such as a secondary accumulator or last-seen value.</td><td style="word-wrap: break-word;">A floating-point running average of user satisfaction scores that is updated each loop iteration.</td></tr>
<tr><td style="word-wrap: break-word;">initial_value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third optional loop-carried variable, available for complex loop bodies needing multiple pieces of evolving state.</td><td style="word-wrap: break-word;">A dictionary holding summary statistics for a dataset (min, max, count) updated on each iteration.</td></tr>
<tr><td style="word-wrap: break-word;">initial_value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fourth optional loop-carried variable. As with other initial_value sockets, it is passed through all iterations and output with its final value.</td><td style="word-wrap: break-word;">An image tensor that is refined slightly each iteration by the loop body.</td></tr>
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
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Final value of the first loop-carried variable after the for-loop finishes all iterations or exits early. The type matches whatever was passed into initial_value1.</td><td style="word-wrap: break-word;">A list of 250 log entry IDs accumulated across all iterations of the loop.</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Final value of the second loop-carried variable. Use in downstream nodes that consume the full result of iterative processing.</td><td style="word-wrap: break-word;">A dictionary like {"mean": 0.87, "std": 0.12} after processing each batch in a dataset.</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Final value of the third loop-carried variable from the last completed iteration.</td><td style="word-wrap: break-word;">The last processed customer record object after looping through a segment.</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Final value of the fourth loop-carried variable, typically used for secondary state or artifacts produced by the loop body.</td><td style="word-wrap: break-word;">A refined prompt template string that has been iteratively tuned over several loop passes.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node builds an internal loop using a while-loop mechanism; very large iteration counts or heavy loop bodies can significantly increase execution time.
- **Limitations**: This node is deprecated; for new pipelines, prefer using Loop Open / Loop Close which provide a more modern and flexible loop abstraction.
- **Behavior**: All initial_value sockets are optional and type-agnostic; however, mismatched or inconsistent types across iterations in the loop body can lead to runtime errors or unexpected results downstream.
- **Behavior**: If an internal error occurs during graph expansion, the node logs the error and returns None for all outputs instead of failing hard, which can mask underlying configuration issues if not monitored.

## Troubleshooting
- **Loop never terminates or runs too many iterations**: Verify that the upstream flow_control from SaltForLoopOpen is configured with the correct start, end, and step values, and that the loop body does not alter the loop counter in unexpected ways.
- **Outputs are all None**: This usually indicates an internal error during loop graph construction. Check the engine logs for an "Error in for loop close" message and verify that your flow_control input is valid and that the underlying SaltWhileLoopClose and related logic nodes are present and correctly configured.
- **Type errors inside the loop body**: If you see type mismatch or unsupported operation errors, confirm that each initial_value socket consistently carries the same type across all iterations and that downstream operations in the loop body support that type.
- **No output changes despite loop running**: Ensure that your loop body actually updates and returns the loop-carried values; SaltForLoopClose only exposes whatever final values the underlying while-loop provides, so a static body will yield unchanged initial_value outputs.
