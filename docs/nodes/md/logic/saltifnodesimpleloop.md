# Do While Loop

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltIfNodeSimpleLoop is a loop controller node built on top of SaltIfNode, providing a higher-level do-while loop abstraction. It starts with "open_" inputs for initial values, then repeatedly feeds back "close_" inputs from the loop body while a condition evaluates to true. The node also exposes per-iteration index and final iteration count, and logs a summary of loop iterations when the loop exits.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltifnodesimpleloop.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to run a set of nodes repeatedly until a condition indicates to stop, such as counting up to a limit, iteratively refining a value, or processing items in batches. Place it as a logical parent at the start of your loop: wire its open_value_* outputs into your loop body nodes, then wire the body’s updated values back into close_value_* along with a condition output that determines whether to continue. Upstream, connect initializers (e.g., SaltIntMathOperation for starting counters or constructing initial state) into open_value_*; downstream, consume the final results from the corresponding false/exit outputs (value_*_false) and, if needed, from count. Often you will pair this node with condition nodes (like integer comparison/condition nodes) to compute the loop condition, and with SaltConditionalMerge or output nodes to handle post-loop logic. Best practice is to clearly separate: (1) initial seed values to open_value_*, (2) per-iteration updates wired back into close_value_*, and (3) exit-time consumers wired from value_*_false and count. Always ensure that each iteration updates the condition deterministically, to avoid infinite loops or premature exits.

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
<tr><td style="word-wrap: break-word;">open_value_1</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Initial value for the first loop channel, used for the first iteration if close_value_1 is not yet available. This is typically the starting value for a counter, accumulator, or state object. Any data type is allowed as long as downstream nodes support it.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">open_value_2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional second initial value channel. Behaves like open_value_1 but for a second piece of state (e.g., a struct with extra metadata). Additional open_value_N inputs appear dynamically as you connect more values.</td><td style="word-wrap: break-word;">{"total": 0, "step": 1}</td></tr>
<tr><td style="word-wrap: break-word;">open_value_3</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Further optional initial value channels; open_value_3 and above are typically hidden until the previous open_value_N is wired. Used when your loop needs to carry three or more parallel values.</td><td style="word-wrap: break-word;">["item-1", "item-2"]</td></tr>
<tr><td style="word-wrap: break-word;">close_value_1</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Feedback value for the first loop channel, usually produced by nodes at the end of the loop body. When present, it overrides open_value_1 for that channel and becomes the per-iteration value on subsequent iterations.</td><td style="word-wrap: break-word;">["10", 0]</td></tr>
<tr><td style="word-wrap: break-word;">close_value_2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Feedback value for the second loop channel. Like close_value_1, it overrides open_value_2 and carries updated state from the loop body each iteration.</td><td style="word-wrap: break-word;">["14", 0]</td></tr>
<tr><td style="word-wrap: break-word;">close_value_3</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Additional feedback channels for higher-indexed values. For each i, close_value_i (if connected) is treated as the current value for that channel in the given iteration, instead of open_value_i.</td><td style="word-wrap: break-word;">["processorNodeId", 1]</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Loop continuation condition. When true (or truthy after coercion), the loop continues to another iteration; when false, the loop exits. Typically wired from a condition-generator node at the end of the loop body. Defaults to true for the initial do iteration if not explicitly set.</td><td style="word-wrap: break-word;">["conditionNodeId", 0]</td></tr>
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
<tr><td style="word-wrap: break-word;">value_1_true</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Inner-loop output for the first data channel, representing the current value that should flow into the loop body on this iteration. In practice, this is what your first body node will receive as input for that channel.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">value_1_false</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Outer-loop (exit) output for the first data channel, representing the final value when the loop has finished. Connect this to downstream nodes that need the completed result of the loop for that channel.</td><td style="word-wrap: break-word;">4</td></tr>
<tr><td style="word-wrap: break-word;">value_2_true</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Inner-loop output for the second data channel, used as the second current value flowing into the loop body each iteration. Mirrors open_value_2/close_value_2 semantics.</td><td style="word-wrap: break-word;">{"total": 10, "step": 1}</td></tr>
<tr><td style="word-wrap: break-word;">value_2_false</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Outer-loop (exit) output for the second data channel, giving the final second-channel state after the loop completes.</td><td style="word-wrap: break-word;">{"total": 20, "step": 1}</td></tr>
<tr><td style="word-wrap: break-word;">value_N_true_false</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Conceptual placeholder for additional dynamic output pairs. For each connected value_N channel, the node exposes corresponding value_N_true (inner-loop) and value_N_false (exit) outputs. The exact set of these outputs is determined dynamically from connected value indices.</td><td style="word-wrap: break-word;">For N = 3, value_3_true might be ["item-1", "item-2"], and value_3_false might be ["item-1", "item-2", "item-3"].</td></tr>
<tr><td style="word-wrap: break-word;">index</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based iteration index for the current execution. Starts at 0 on the first iteration and increments by 1 each time the loop body runs. Useful for counters, debugging, or indexing into arrays inside the loop.</td><td style="word-wrap: break-word;">2</td></tr>
<tr><td style="word-wrap: break-word;">count</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Loop completion count output. During non-final iterations (condition true) this is 0; on the exit iteration (when condition is false) it equals the last completed iteration index, effectively indicating how many iterations ran.</td><td style="word-wrap: break-word;">4</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each iteration is logged and, when the loop ends, a formatted iteration summary is written to backend logs. Very large iteration counts can generate a lot of log output and increase runtime overhead; consider capping iterations or reducing logging in production workflows.
- **Limitations**: The underlying implementation supports a fixed maximum number of value channels (defined in the engine, typically up to 100). You should keep the number of parallel value_* streams well below this limit for clarity and reliability.
- **Behavior**: For each potential channel index, if neither open_value_i nor close_value_i is connected, the node still allocates a slot and returns null/None for that channel’s outputs. You may therefore see extra null outputs if you skip indices; downstream nodes should ignore unused slots.
- **Behavior**: Condition inputs are coerced to boolean: strings like "true"/"false" are interpreted case-insensitively, and other non-boolean types are cast with standard truthiness rules. Passing numbers, objects, or non-empty strings may cause the loop to continue unintentionally.
- **Behavior**: For engine-level routing, the node conceptually emits values on both "true" and "false" branches, but the engine uses stored condition metadata to decide which branch actually propagates. Do not rely on one branch being null as your control signal; use the condition itself to control logic.

## Troubleshooting
- **Loop never exits**: Symptom: workflow appears stuck or logs show a very long series of iterations. Likely cause: the condition stays truthy because your condition node never returns false or returns a non-boolean value that casts to true. Fix: ensure the condition generator returns a proper BOOLEAN false when the stop criterion is met, and avoid using non-boolean condition values.
- **Loop exits immediately**: Symptom: only one iteration runs and count remains 0 or very small. Likely cause: the condition is false (or coerces to false) on the first iteration, often because the condition input is uninitialized or incorrectly wired. Fix: check that your condition node is connected correctly and that its initial output is true for the first iterations.
- **Unexpected null outputs**: Symptom: some value_N_true or value_N_false outputs are null/None when data is expected. Likely cause: the corresponding open_value_N and close_value_N were never connected, or you skipped an index when wiring. Fix: connect both sides for any index you intend to use and avoid gaps in the index sequence, or explicitly ignore unused outputs.
- **Index or count seems off**: Symptom: index/count do not match your mental model, especially with nested or multiple loops. Cause: index is zero-based and per-loop-instance, and count only reflects the final iteration index on exit. Fix: add 1 to index if you need human-friendly iteration numbers, and verify you are reading index/count from the intended loop node in complex workflows.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/logic/saltifnodesimpleloop/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

