# Loop Open

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loop Open creates and initializes a loop that can behave like a for-loop, a while-loop, or a hybrid of both. It sets up loop metadata (start, step, end, index and finished status) and evaluates a condition expression to decide whether the loop body should execute. It outputs loop status plus up to five data channels that can be preserved and updated across iterations when wired to a Loop Close node.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to repeat part of a workflow multiple times under controlled conditions. Typical scenarios include batching over a list of items, running a fixed number of refinement steps, or iterating until a quality threshold is met. Place Loop Open before the nodes you want to repeat, connect its LOOP output and data/aux outputs into your loop body, and close the loop with a Loop Close node.

In a basic pattern, upstream nodes prepare initial data and optional aux/aux2/aux3/aux4 values (such as lists of records, images, or running metrics). The LOOP output connects directly to the LOOP input of SaltLoopClose. The data and auxiliary outputs should be wired to the corresponding inputs on SaltLoopClose so that any modifications made within the loop body are preserved from one iteration to the next. For a for-loop style pattern, set integer start, step, and end so the index walks a defined range and keep condition simple (for example, "True"). For a while-loop style, set step to 0 and encode your stop logic purely in condition. For hybrid behavior, use both a non-zero step and a condition so the loop stops either when the index passes the bound or the condition fails.

Best practices: keep condition expressions simple and based on loop variables and known data fields, avoid extremely large iteration ranges to reduce resource consumption, and ensure that each data channel you want to persist is consistently wired between Loop Open and Loop Close. Use index_override only when you have a clear reason to jump to a specific iteration (for example, resuming from a stored index or skipping ahead).

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
<tr><td style="word-wrap: break-word;">start</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Initial value of the loop counter. The first iteration uses this value as index. Together with step and end it defines the iteration range.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">step</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Increment applied to the loop counter on each iteration. Positive values count upwards, negative values count downwards, and 0 is typically used when you drive termination purely via the condition expression.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">end</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Target boundary for the loop counter. For positive step, the loop is considered finished when end - index <= 0; for negative step, when end - index >= 0. This acts as an upper or lower bound on the number of iterations.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A boolean expression (as text) evaluated against loop variables and available data. The loop executes only if this evaluates to True on Loop Open. You can reference fields such as start, end, step, index, finished, and any data passed via data or aux slots, using simple Python-like syntax.</td><td style="word-wrap: break-word;">index < end and (aux is None or aux['quality'] < 0.95)</td></tr>
<tr><td style="word-wrap: break-word;">index_override</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Optional manual override for the current loop index. When provided and not None, this value is used as index instead of the automatic progression from start and step. This allows custom stepping or skipping to a specific iteration.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Primary data payload that travels through the loop. Can be any type (for example a list of tasks, a batch object, or an in-progress aggregate). If connected through Loop Close, this value can change each iteration and will be carried forward.</td><td style="word-wrap: break-word;">[{"id": 1, "prompt": "product description"}, {"id": 2, "prompt": "support email"}]</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First auxiliary data channel to iterate along with data. Intended for secondary state such as metrics, options, or counters that you want to preserve across iterations.</td><td style="word-wrap: break-word;">{"processed_count": 0, "average_score": 0.0}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second auxiliary data slot. Behaves like aux but provides another independent channel for loop-carried state.</td><td style="word-wrap: break-word;">{"images": ["gs://my-bucket/run/img_0001.png"], "metadata": {"batch": 1}}</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third auxiliary data slot. Useful for keeping configuration, debug traces, or other side-information separate from the main data.</td><td style="word-wrap: break-word;">{"config": {"temperature": 0.7, "max_tokens": 512}}</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fourth auxiliary data slot. Use this if you need up to four separate auxiliary objects flowing through the loop in parallel with data.</td><td style="word-wrap: break-word;">{"loop_history": [{"index": 1, "score": 0.82}]}</td></tr>
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
<tr><td style="word-wrap: break-word;">LOOP</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Dictionary describing the current loop state. It includes keys such as id, start, end, step, index, finished, last_id, and condition_open. Connect this to the LOOP input of Loop Close to coordinate continuation and termination, or inspect it with monitoring nodes for debugging.</td><td style="word-wrap: break-word;">{"id": "c7e2b3a0-4c10-4e23-a1bd-9d1f6ca0c001", "start": 1, "end": 10, "step": 1, "index": 3, "finished": false, "last_id": "node-42", "condition_open": true}</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The current value of the main data payload at this point in the loop. Typically passed into the loop body for processing, then into Loop Close so any updates are preserved for the next iteration.</td><td style="word-wrap: break-word;">{"id": 2, "prompt": "support email", "draft": "Thank you for reaching out..."}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Current value of the first auxiliary data channel. Use this for running counters, aggregate metrics, or other side-information that is updated each iteration.</td><td style="word-wrap: break-word;">{"processed_count": 2, "average_score": 0.88}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Current value of the second auxiliary data channel. Its structure is user-defined and should match what your loop body and Loop Close expect.</td><td style="word-wrap: break-word;">{"images": ["gs://my-bucket/run/img_0001.png", "gs://my-bucket/run/img_0002.png"]}</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Current value of the third auxiliary data channel. Often used for configuration samples, intermediate model parameters, or diagnostic details.</td><td style="word-wrap: break-word;">{"config": {"temperature": 0.7, "max_tokens": 512}, "last_latency_ms": 230}</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Current value of the fourth auxiliary data channel, commonly used for tracking per-iteration history or logs.</td><td style="word-wrap: break-word;">{"loop_history": [{"index": 1, "score": 0.82}, {"index": 2, "score": 0.91}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each iteration effectively re-executes the nodes between Loop Open and Loop Close; deep or expensive loop bodies combined with many iterations can increase runtime and memory usage noticeably.
- **Limitations**: The condition string is evaluated in a restricted safe environment and may not support all Python constructs; use straightforward boolean expressions based on known loop variables and data fields.
- **Behavior**: If the evaluated condition on Loop Open is False, the node blocks execution for the loop body, so downstream nodes inside the loop will not run for that iteration.
- **Behavior**: When index_override is used repeatedly without a termination plan (for example, jumping backwards each time), the loop can behave like an unbounded loop until external loop guards or platform limits intervene.
- **Limitations**: Only five loop-carried data channels are exposed (data, aux, aux2, aux3, aux4); for more complex needs, group related values into dictionaries or lists.

## Troubleshooting
- **Symptom: Loop body never runs**: Ensure the condition field is not empty and that it evaluates to True for the initial index and data. Also verify that start, step, and end are configured so the loop is not already marked as finished before the first iteration.
- **Symptom: Loop stops earlier than expected**: Inspect both your condition expression and numerical bounds. With a positive step and end less than or equal to start, the loop may immediately be considered finished, or a too-strict condition (such as index <= end combined with a large step) may skip past the desired range.
- **Symptom: State resets every iteration**: Make sure the data and auxiliary outputs from Loop Open are wired into the matching inputs of Loop Close, and that Loop Close is correctly configured so its outputs are used as inputs for the next iteration.
- **Symptom: Errors in condition evaluation**: If errors appear or the node behaves as if the condition is always False, simplify the condition string and avoid unsupported operations. Reference only variables you know exist in the loop context, such as index, start, end, step, finished, and fields within data or aux.
