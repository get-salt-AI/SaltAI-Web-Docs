# While Loop Open (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node marks the opening of a while-style loop in a Salt logic graph. It accepts a Boolean condition and up to five initial values, then outputs a flow-control token plus those values as the initial loop state. The node itself does not control repetition; it only initializes and forwards state to be coordinated with a corresponding loop close node.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltwhileloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Place this node at the start of a while loop when you need to repeatedly process data until some condition becomes false. It is designed to be paired with `SaltWhileLoopClose`, which receives the flow-control token and updated values at the end of each iteration and decides whether to trigger another pass. Upstream, connect any required initial state (counters, accumulators, context) to `initial_value0`–`initial_value4`. Inside the loop, downstream nodes read `value0`–`value4`, update them, and send them toward `SaltWhileLoopClose`, which evaluates its own `condition` input to continue or stop. For new workflows, prefer `Loop Open` / `Loop Close` or `JSON Loop Open`, but this node remains important for understanding and maintaining existing graphs that still use the deprecated while-loop pair.

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
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Initial Boolean indicating that the loop conceptually starts in an active state. This value is accepted but does not directly control iterations; actual loop continuation is handled on `SaltWhileLoopClose` via its `condition` input.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">initial_value0</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First initial value carried into the loop, output as `value0`. Accepts any supported data type (number, string, image, object). If omitted, `value0` will be null or empty on the first iteration.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second initial value, exposed as `value1`. Use this for secondary state such as a session identifier, additional counter, or auxiliary configuration.</td><td style="word-wrap: break-word;">session_context_123</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third initial value, exposed as `value2`. Commonly used for structured objects that track progress or configuration across iterations.</td><td style="word-wrap: break-word;">{'processed_items': 0, 'target_items': 50}</td></tr>
<tr><td style="word-wrap: break-word;">initial_value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fourth initial value, exposed as `value3`. Provides another channel for loop-carried state, such as a list of remaining tasks or items.</td><td style="word-wrap: break-word;">['task_a', 'task_b', 'task_c']</td></tr>
<tr><td style="word-wrap: break-word;">initial_value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fifth initial value, exposed as `value4`. Use this when you need an additional independent state variable to propagate and update across iterations.</td><td style="word-wrap: break-word;">{'batch_id': 'batch-2024-04-20', 'retry_count': 0}</td></tr>
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
<tr><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">A flow-control token linking this node to its paired `SaltWhileLoopClose`. This token must be connected to the close node’s `flow_control` input so the execution engine recognizes and manages the loop context.</td><td style="word-wrap: break-word;">FLOW_CONTROL_TOKEN(stub)</td></tr>
<tr><td style="word-wrap: break-word;">value0</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of `initial_value0` on the first iteration, then the updated value for this slot on subsequent iterations. Type is unconstrained and matches whatever state you carry here.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of `initial_value1`, then its updated value in later iterations. Often used for contextual strings, IDs, or other auxiliary state.</td><td style="word-wrap: break-word;">session_context_123</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of `initial_value2`. Ideal for structured state such as progress trackers or configuration maps that change as the loop runs.</td><td style="word-wrap: break-word;">{'processed_items': 0, 'target_items': 50}</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of `initial_value3`, then its updated value each iteration. Another general-purpose channel for loop-carried data.</td><td style="word-wrap: break-word;">['task_a', 'task_b', 'task_c']</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of `initial_value4`. Provides a fifth independent state channel that you can update within the loop body and reuse on each pass.</td><td style="word-wrap: break-word;">{'batch_id': 'batch-2024-04-20', 'retry_count': 0}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node itself is lightweight and primarily forwards data; overall cost depends on the complexity and number of nodes in the loop body and on how many iterations the close node’s condition allows.
- **Limitations**: This node is explicitly marked as deprecated, maintained for backward compatibility. For new workflows, use `Loop Open` / `Loop Close` or `JSON Loop Open` instead and plan to migrate older graphs.
- **Behavior**: The `condition` input here is not what determines repeated execution; effective loop continuation is controlled by `SaltWhileLoopClose.condition`, so treat this node as a state initializer rather than the loop controller.
- **Behavior**: All `initial_valueN` inputs are optional. If you leave any unconnected, the corresponding `valueN` output will be null/empty on the first iteration, and downstream nodes must handle that safely.
- **Behavior**: On internal errors, the node logs the error and returns a stub flow-control token with `None` values for all outputs so the graph can continue running, albeit without valid initial loop state.

## Troubleshooting
- **Loop executes only once**: Symptom: The loop body runs a single time even though `condition` on this node is true. Cause: Iteration is actually controlled by `SaltWhileLoopClose.condition`. Fix: Connect the correct dynamic Boolean to `SaltWhileLoopClose.condition` and verify it stays true for as long as more iterations are required.
- **Loop close not recognized as part of this loop**: Symptom: `SaltWhileLoopClose` behaves like a regular node or shows issues about missing flow control. Cause: Its `flow_control` input is not wired to this node’s `FLOW_CONTROL` output. Fix: Connect `SaltWhileLoopOpen.FLOW_CONTROL` directly to `SaltWhileLoopClose.flow_control` and avoid mixing tokens across different loop opens.
- **Null or empty values on first iteration**: Symptom: One or more `valueN` outputs are null or empty during the first iteration. Cause: The corresponding `initial_valueN` inputs were never provided. Fix: Supply explicit initial values for each required state channel or add null-checking and defaulting logic in downstream nodes.
- **Deprecation-related concerns**: Symptom: Tooling or documentation flags this node as deprecated. Cause: `SaltWhileLoopOpen` is a legacy loop construct. Fix: Gradually replace it with `Loop Open` / `Loop Close` or `JSON Loop Open`, mapping your use of `initial_value0`–`initial_value4` to equivalent state channels in the newer loop nodes.
