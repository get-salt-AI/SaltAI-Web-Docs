# Loop Open

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loop Open initializes a loop with configurable start, step, end, and a condition expression, and emits a LOOP status dictionary along with data and auxiliary payloads for each iteration. It can act as a for-loop (bounded by start/step/end), a while-loop (terminated by a condition), or a hybrid of both. This node must be paired with Loop Close, which consumes the LOOP output and manages the iteration over the enclosed subgraph.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use Loop Open when you need to repeat a section of your workflow multiple times while preserving evolving state. Place Loop Open before the nodes that should form the loop body, and connect its LOOP output to the LOOP input of a Loop Close node; everything between them will be executed on each iteration. Configure `start`, `step`, and `end` for fixed-count loops (for example, indices 1 through 10), or set a `condition` expression to control execution like a while-loop based on loop variables and data. The `data` and `aux` / `aux2` / `aux3` / `aux4` inputs allow you to pass arbitrary objects (lists of prompts, configuration dicts, accumulators, etc.) into the loop; these values should be wired through Loop Close so that any changes made in the body persist to the next iteration.

Upstream nodes typically generate or load the initial `data` and auxiliary state, such as a list of prompts to iterate, initial counters, or configuration parameters. Downstream, Loop Close is required to complete the loop, deciding whether to continue or stop based on LOOP status and possibly its own condition, and returning updated data back into the iteration chain. Monitoring or inspection nodes can read the LOOP dictionary to access index, bounds, and flags for logging, branching, or adaptive behavior. This node is a good fit for batched processing of collections, iterative refinement pipelines, or algorithmic loops like stepping through time steps or parameter sweeps.

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
<tr><td style="word-wrap: break-word;">start</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Initial value of the loop counter. Sets the first `index` value for the loop. Any integer is allowed; 0 or 1 are common for simple counters.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">step</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Amount added to the index each iteration. Positive values count up, negative values count down. If 0, the index does not change and the loop behaves like a pure while-loop governed only by the condition expression.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">end</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">End bound for the loop. With a non-negative step, the loop is considered finished when `index >= end`. With a negative step, it is finished when `index <= end`.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Boolean expression evaluated each iteration using an environment that includes loop status (such as `id`, `start`, `end`, `step`, `index`, `finished`) as well as stored logic data and current input values. If the expression evaluates to True, the loop proceeds; if False, the loop is blocked and marked finished. An empty string is treated as True.</td><td style="word-wrap: break-word;">index < end and finished == False</td></tr>
<tr><td style="word-wrap: break-word;">index_override</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional manual override for the current index. When provided and not None, this value is used as the loop `index` for the iteration, letting you perform custom stepping or jumps. If omitted, the index defaults to `start` for the first iteration and is normally managed by the loop.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Primary data payload carried through the loop. This can be any type, such as strings, lists, dicts, or references to larger artifacts. If connected to Loop Close and back, modifications made in the loop body are preserved across iterations.</td><td style="word-wrap: break-word;">['prompt 1: sunset over mountains', 'prompt 2: cyberpunk city at night', 'prompt 3: underwater coral reef']</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">First auxiliary data slot for any additional state you want to retain across iterations, such as configuration flags, per-iteration metrics, or intermediate results.</td><td style="word-wrap: break-word;">{'sampler': 'ddim', 'steps': 30}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Second auxiliary slot used when your loop requires multiple separate state channels (for example, counters and error statistics).</td><td style="word-wrap: break-word;">{'processed_count': 7}</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Third auxiliary slot for additional loop-related state used in complex or deeply stateful loops.</td><td style="word-wrap: break-word;">{'current_temperature': 0.85}</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Fourth auxiliary slot, giving you up to four independent auxiliary channels to structure your loop state.</td><td style="word-wrap: break-word;">{'last_generated_image_id': 'img_2024_06_15_0012'}</td></tr>
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
<tr><td style="word-wrap: break-word;">LOOP</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Dictionary containing the current loop status. Typical keys include `id` (stable loop id), `start`, `end`, `step`, `index`, `finished` (True when loop is considered done), `last_id` (identifier of the last node in the iteration chain), and `condition_open` when the open-side condition evaluated to True. Must be connected to the LOOP input of a Loop Close node, and can also be inspected by diagnostic nodes.</td><td style="word-wrap: break-word;">{'id': 173, 'start': 1, 'end': 10, 'step': 1, 'index': 4, 'finished': False, 'last_id': 'node-3f9c2d10-7b8a-4ad6-9b61-2fef93a1b9cd', 'condition_open': True}</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Current value of the primary data payload for this iteration. It matches the `data` input on the first iteration and then reflects updates passed through Loop Close on later iterations.</td><td style="word-wrap: break-word;">prompt 2: cyberpunk city at night, rain, neon reflections</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Current value of the first auxiliary data slot, forwarded between Loop Open and Loop Close each iteration.</td><td style="word-wrap: break-word;">{'sampler': 'euler_a', 'steps': 25}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Current value of the second auxiliary slot, typically used for counters, summaries, or secondary state.</td><td style="word-wrap: break-word;">{'processed_count': 3, 'failed_count': 0}</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Current value of the third auxiliary slot for this iteration, useful for metrics or control variables.</td><td style="word-wrap: break-word;">{'running_loss': 0.3421}</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Current value of the fourth auxiliary slot, carried through iterations in the same way as the other aux channels.</td><td style="word-wrap: break-word;">{'last_generated_image_id': 'img_2024_06_15_0015'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each iteration clones and executes the subgraph between Loop Open and Loop Close; large loop bodies or high iteration counts can increase memory usage and runtime significantly.
- **Limitations**: Misconfigured loops that never satisfy the termination condition or never reach the end bound may be forcibly stopped by global loop guards after a maximum number of iterations.
- **Behavior**: If the condition at Loop Open evaluates to False, all outputs become blocking markers that prevent downstream execution for that path, and the loop is treated as finished from the open side.
- **Behavior**: Aggressive use of `index_override` (especially jumping backwards without a separate termination condition) can cause effective infinite loops or skipped iterations; use it sparingly and with clear logic.

## Troubleshooting
- **Loop appears to run forever or hits a guard limit**: Check that `step` moves the index toward `end` and that your `condition` expression becomes False under some realistic state; otherwise, adjust step direction or condition logic.
- **Nodes after Loop Open never execute**: Your condition may be evaluating to False on the first evaluation. Temporarily set `condition` to `True` or a minimal expression and use monitoring nodes to inspect which variables are available.
- **State does not persist between iterations**: Ensure `data` and `aux*` outputs from Loop Open are wired to the corresponding inputs of Loop Close, and that Loop Close passes its outputs back into the loop. Missing or misconnected links will cause values to reset or not update as expected.
- **Condition evaluation failures**: If you encounter errors about condition evaluation, check that your expression only references variables that exist in the evaluation context and uses operations valid for their types (for example, avoid arithmetic on non-numeric types).
