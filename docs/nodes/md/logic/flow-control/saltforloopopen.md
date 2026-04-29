# For Loop Open (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node starts a counted loop that will run a specified number of times. You set how many iterations should remain and optionally provide initial values that are passed into the loop body on the first iteration. It outputs a flow control handle, the remaining-iterations counter, and auxiliary values to be consumed by the loop body and eventually closed with a matching For Loop Close node.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltforloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use For Loop Open (Deprecated) at the beginning of a sequence that needs to run a fixed number of times, such as batching API calls, iteratively refining data, or processing a list with a known length. Set the remaining input to the total number of iterations you want the loop to run; this value becomes the loop counter used internally. Any values you want available inside the loop body for the first iteration (for example, an accumulator value, a list, or a configuration object) should be wired into the initial_value inputs. Typically, you connect the flow_control and the accompanying remaining and value* outputs to nodes that make up your loop body, and then complete the loop with a For Loop Close (Deprecated) node. Upstream, you might feed constants, configuration, or precomputed data structures into the remaining and initial_value* inputs. Downstream, the corresponding close node consumes the flow control signal and values to manage iteration and to produce the final aggregated outputs. Because this node is deprecated, prefer using Loop Open and Loop Close for new workflows, but keep this pair in place for maintaining existing graphs.

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
<tr><td style="word-wrap: break-word;">remaining</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The initial number of iterations the loop should run. Must be an integer between 0 and 100000. A value of 0 results in a loop that does not run at all; positive values control how many times the loop body will execute.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First optional value passed into the loop on the first iteration. Can be any supported data type (text, number, image, JSON-like structure, etc.). This becomes value1 on the output and is often used as a working variable or accumulator.</td><td style="word-wrap: break-word;">{"items": ["doc-001", "doc-002", "doc-003"], "index": 0}</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second optional value passed into the loop on the first iteration. Use this to pass additional state or configuration the loop body needs.</td><td style="word-wrap: break-word;">{"threshold": 0.8, "max_retries": 3}</td></tr>
<tr><td style="word-wrap: break-word;">initial_value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third optional initial value for the loop body. Any further initial_value* inputs (if exposed) behave the same way as extra pass-through values.</td><td style="word-wrap: break-word;">[]</td></tr>
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
<tr><td style="word-wrap: break-word;">flow_control</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">Loop control handle used together with the matching For Loop Close node to manage iteration. Must be wired through the loop body to the For Loop Close node so the loop can progress and terminate correctly.</td><td style="word-wrap: break-word;">A FLOW_CONTROL token that is connected into the For Loop Close node to define the loop structure.</td></tr>
<tr><td style="word-wrap: break-word;">remaining</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Current remaining iteration count at loop open. On the first pass this usually matches the remaining input unless overridden by initial_value0. Used by downstream logic, especially For Loop Close, to decide whether to continue iterating.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">First pass-through value corresponding to initial_value1. Delivered to the loop body as the starting value for that variable and typically updated on each iteration in the loop close logic.</td><td style="word-wrap: break-word;">{"items": ["doc-001", "doc-002", "doc-003"], "index": 0}</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Second pass-through value corresponding to initial_value2. Often used for configuration or auxiliary data that may or may not change across iterations.</td><td style="word-wrap: break-word;">{"threshold": 0.8, "max_retries": 3}</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Third pass-through value corresponding to initial_value3. Any additional value* outputs behave similarly, passing more initial payloads into the loop body.</td><td style="word-wrap: break-word;">[]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Large remaining values (for example, tens of thousands of iterations) will increase runtime and resource usage; consider batching or limiting iterations where possible.
- **Limitations**: This node is deprecated; for new designs prefer Loop Open and Loop Close. Do not mix deprecated loop nodes with the newer loop nodes inside the same logical loop.
- **Behavior**: On internal errors when constructing the loop, the node returns a stub flow control value, the remaining count, and None for extra values, which can cause the loop to effectively be skipped.
- **Behavior**: If initial_value0 is set, it overrides the remaining input. Unexpected iteration counts often indicate something upstream is indirectly setting this hidden value.

## Troubleshooting
- **Loop never runs (0 iterations)**: If the loop body never executes, confirm that remaining is greater than 0 and that no upstream node is setting initial_value0 to 0. Also verify that the For Loop Close node is present and connected to the flow_control output.
- **Infinite or very long loop**: If execution appears stuck or extremely slow, check that remaining is within a reasonable range and that the For Loop Close node correctly updates the loop state. Miswired close logic can prevent the remaining counter from reaching zero.
- **Missing or None values inside loop**: If some values appear as None in the loop body, ensure that the corresponding initial_value* inputs are connected and that the For Loop Close node expects the same number and ordering of value* sockets.
- **Loop behaves like passthrough**: If the flow runs only once with no real looping, the internal loop graph may have failed to expand. Confirm both For Loop Open and For Loop Close are present, properly connected via flow_control, and not mixed with non-matching loop node types.
