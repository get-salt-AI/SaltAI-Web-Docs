# For Loop Open (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node initializes a for-style loop, preparing an iteration counter and optional auxiliary values to be threaded through each pass of the loop body. Internally it expands into a loop control structure using while-loop semantics but exposes a simple remaining-based interface. It is deprecated in favor of newer loop nodes, but remains available to support existing Salt workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltforloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the beginning of a looped section when you want to execute a group of nodes a fixed number of times. Connect an integer to remaining to set the desired number of iterations; 0 means the loop body will not run. Optionally connect initial_value1, initial_value2, and further initial_valueN inputs (as exposed by your environment) to seed state such as counters, collections, partial results, or configuration objects that will persist across loop iterations. The flow_control output must be wired into the corresponding loop-close node (typically "For Loop Close (Deprecated)") which will handle decrementing the counter and determining whether to execute another iteration. Place this node downstream of any setup logic that calculates how many iterations are needed and assembles initial state, and upstream of the loop body that should repeat. For new designs, prefer using Loop Open and Loop Close nodes, but keep this node unchanged when maintaining legacy graphs that already rely on it.

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
<tr><td style="word-wrap: break-word;">remaining</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of iterations the loop should perform. Must be a non-negative integer; values are constrained to the range 0–100000. This becomes the initial remaining-iterations counter for the loop and controls how many times the downstream block will run.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First user-facing auxiliary value passed into the loop. Any supported data type (text, numbers, objects, collections, model outputs) is allowed. This value is exposed to the loop body as value1 and can be updated across iterations by downstream nodes.</td><td style="word-wrap: break-word;">["doc_001.json","doc_002.json","doc_003.json"]</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second auxiliary value seeded into the loop. Use this for separate state such as configuration, accumulators, or metadata that must be accessible in every iteration. It is provided to the loop body as value2.</td><td style="word-wrap: break-word;">{"batch_size":4,"max_retries":3}</td></tr>
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
<tr><td style="word-wrap: break-word;">flow_control</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">Special control token that represents the open loop context. This must be connected to a compatible loop-closing node so Salt can expand and execute the loop body multiple times. It does not carry user data directly but orchestrates how the graph is repeated.</td><td style="word-wrap: break-word;">A FLOW_CONTROL handle wired into a For Loop Close (Deprecated) node, causing the intermediate graph segment to execute 10 times.</td></tr>
<tr><td style="word-wrap: break-word;">remaining</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The remaining-iterations count as seen at the loop entry. On first use this equals the configured remaining (or initial_value0 if provided). It is typically consumed and updated by the loop-close node as the loop proceeds.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop-carried value corresponding to initial_value1. It is passed to nodes inside the loop body on each iteration and may be modified by downstream logic before being fed into the next iteration via the loop-close node.</td><td style="word-wrap: break-word;">["doc_001.json","doc_002.json","doc_003.json"]</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop-carried value corresponding to initial_value2. As with value1, this is available inside the loop body on each iteration and can store configuration, running statistics, or aggregation state.</td><td style="word-wrap: break-word;">{"batch_size":4,"max_retries":3}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: High iteration counts (near the 100000 upper bound) can cause very large expanded graphs and heavy execution time and memory use; choose the smallest iteration count that meets your needs.
- **Limitations**: This node is explicitly deprecated and may be removed or superseded in future releases. When designing new pipelines, use the newer Loop Open and Loop Close nodes instead.
- **Behavior**: If a hidden initial_value0 is set, it overrides the visible remaining input as the internal loop condition, which can cause unexpected iteration counts if overlooked.
- **Behavior**: On internal expansion errors, the node logs an error and returns None for all auxiliary outputs (value1, value2, etc.) while still providing a flow_control and remaining value. Downstream nodes should be prepared to handle None in these auxiliary positions.

## Troubleshooting
- **Common Error 1**: Loop body does not run or runs zero times. Verify that remaining is greater than 0 and that initial_value0 (if used) is not 0 or another falsy value overriding the condition. Also confirm that flow_control is wired into a compatible loop-close node.
- **Common Error 2**: Loop-close node reports missing or invalid FLOW_CONTROL. Ensure the flow_control output of For Loop Open (Deprecated) is connected directly to a matching closing node (such as For Loop Close (Deprecated)) and do not mix deprecated and new-style loop nodes in the same loop.
- **Common Error 3**: value1 or other auxiliary values appear as None inside the loop. Check that the corresponding initial_value inputs are connected and of the expected type. If configuration is correct, inspect logs for an "Error in for loop open" message indicating an internal expansion failure that caused the node to substitute None values.
