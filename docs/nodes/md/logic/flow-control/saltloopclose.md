# Loop Close

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loop Close works with Loop Open to implement flexible for/while-style loops in a Salt workflow. It inspects the loop status from Loop Open, evaluates a stop/continue condition, and either schedules the next iteration by cloning the loop’s internal subgraph or finalizes the loop and returns the final data values. The node signals completion via its FINISHED? output and propagates accumulated data (data and aux slots) out of the loop.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use Loop Close whenever you need to terminate or continue a loop that was started with Loop Open. First, add a Loop Open node and connect its LOOP output directly into the LOOP input of Loop Close; all nodes between these two become the loop body. Inside this loop body, route any state you want to persist across iterations (such as counters, image batches, JSON documents) via the data and aux/aux2/aux3/aux4 connections from Loop Open, through your processing nodes, and back into Loop Close. On each iteration, Loop Close receives the current loop status (including index, start, end, step, finished) via LOOP and evaluates the supplied condition expression. If LOOP.finished is true or the condition evaluates to false, Loop Close stops looping, emits FINISHED? = true, and outputs the final data/aux values; connect downstream aggregation, storage, or notification nodes to these outputs. If the loop should continue, Loop Close dynamically recreates the subgraph between Loop Open and Loop Close, updates the Loop Open index and other metadata, and forwards the latest data and aux values to drive the next iteration. Typical upstream nodes are SaltLoopOpen and processing nodes inside the loop; typical downstream nodes are any nodes that only need to run once the loop is complete. Keep the loop body minimal, avoid unbounded growth in state, and use simple condition expressions referencing LOOP fields (index, start, end, step, finished) and your aux/data values.

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
<tr><td style="word-wrap: break-word;">LOOP</td><td>True</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Structured loop status object produced by Loop Open. This dictionary-like value includes metadata such as id, start, end, step, current index, finished flag, and last_id (the last Loop Open node id), plus any additional entries from logic storage or upstream nodes. It must be connected directly from the LOOP output of a Loop Open node for the loop to function correctly.</td><td style="word-wrap: break-word;">{"id":174398120,"start":1,"end":10,"step":1,"index":5,"finished":false,"last_id":42}</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Boolean expression (as a string) evaluated against the loop context to decide whether to keep iterating. It is evaluated with access to LOOP fields (such as index, start, end, step, finished), global logic storage values, and any aux/data or other keyword inputs. If this evaluates to false or LOOP.finished is true, the loop terminates. An empty string is treated as a truthy default, meaning the loop relies solely on LOOP.finished.</td><td style="word-wrap: break-word;">index < end and not finished and aux["error_count"] < 3</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td>False</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Primary stateful payload passed through each loop iteration. This can be any supported Salt data type (for example lists, dicts, images, text). On continuation, Loop Close forwards this value into the next Loop Open iteration; on termination, the final value appears on the data output. For it to behave as loop state, wire Loop Open’s data output through your loop-body nodes back into this input.</td><td style="word-wrap: break-word;">["doc_001.pdf","doc_002.pdf","doc_003.pdf"]</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Auxiliary state slot for additional loop-scoped data. Any value passed here is preserved across iterations if it flows from Loop Open through your processing nodes and back into this input. This is typically used for counters, accumulated results, error tracking, or side-channel metadata.</td><td style="word-wrap: break-word;">{"processed_count":5,"error_count":1,"results":["ok","ok","retry","ok","ok"]}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Second auxiliary data slot for multi-channel loop state. Behaves like aux: values are carried from Loop Open through the loop body back into Loop Close and forwarded on continuation. Leave unconnected if not needed.</td><td style="word-wrap: break-word;">{"current_batch_id":"run-2024-03-01-batch-1"}</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Third auxiliary data slot for separate loop-related state such as metrics or parallel lists that you want to keep distinct from other aux channels.</td><td style="word-wrap: break-word;">[0.92,0.88,0.95,0.90,0.93]</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Fourth auxiliary data slot for extra metadata, diagnostics, or advanced bookkeeping across iterations.</td><td style="word-wrap: break-word;">{"alerts_triggered":["low_confidence_item_3"]}</td></tr>
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
<tr><td style="word-wrap: break-word;">FINISHED?</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True when the loop has fully completed (either all iterations ran or the close-side condition evaluated to false). Downstream nodes should typically gate their execution on this signal being true before performing final aggregation, storage, or notification steps.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Final value of the data stream after the loop finishes. This is the last data object that flowed through the loop body and back into Loop Close before termination, and is generally treated as the main result of the loop.</td><td style="word-wrap: break-word;">["doc_001_processed.json","doc_002_processed.json","doc_003_processed.json"]</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Final value of the primary auxiliary state once the loop is done. Use this output to retrieve accumulated metrics, error information, or other state that you tracked in aux during the loop.</td><td style="word-wrap: break-word;">{"processed_count":10,"error_count":2,"failed_ids":["doc_004.pdf","doc_007.pdf"]}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Final state of the second auxiliary channel. If unused, this is typically null or an empty value.</td><td style="word-wrap: break-word;">{"mean_confidence":0.91}</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Final state of the third auxiliary channel, representing any additional loop-related data you tracked in that slot.</td><td style="word-wrap: break-word;">["checkpoint_5.ref","checkpoint_10.ref"]</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Final state of the fourth auxiliary channel, often used for debugging traces, per-iteration diagnostics, or extra metadata.</td><td style="word-wrap: break-word;">{"debug_trace_len":50}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Loop Close triggers dynamic graph expansion every time it decides to continue the loop; each iteration clones the subgraph between Loop Open and Loop Close. Large loop bodies or high iteration counts can significantly increase execution time and memory usage, so keep the loop body small and efficient.
- **Limitations**: The LOOP input must come from a compatible Loop Open node; arbitrary values or a mismatched structure will cause incorrect behavior or failures. The execution engine also enforces iteration guardrails to prevent infinite or runaway loops.
- **Behavior**: The condition string is evaluated using a safe expression evaluator against a context built from the LOOP dictionary, global logic storage, and all keyword arguments (including aux/data). Empty or non-string conditions are normalized to a truthy default, which can make the loop rely only on LOOP.finished; define explicit conditions to avoid unintentionally unbounded loops.
- **Behavior**: When the loop continues, Loop Close returns an internal structure instructing the engine to expand the next iteration rather than immediately emitting final outputs. Nodes downstream of Loop Close should typically act only when FINISHED? is true, since intermediate iterations are handled via graph expansion and not as standard downstream calls.
- **Limitations**: This node depends on execution-inversion and dynamic graph-cloning support in the runtime. Older or incompatible engine versions may fail with errors indicating that the loop system cannot be created and will require an engine upgrade.

## Troubleshooting
- **Loop never terminates**: If the workflow appears to run the loop indefinitely, verify that your condition expression eventually becomes false and that Loop Open’s start/step/end values or your aux/data updates guarantee progress. Confirm that variable names in the condition (such as index, end, finished) are spelled correctly and refer to actual fields in LOOP or your state.
- **Immediate termination**: If FINISHED? is true on the first run and the loop body runs once or not at all, either LOOP.finished is already true (for example, due to start/end/step misconfiguration) or your condition expression evaluates to false on the first iteration. Temporarily set condition to "True" and then gradually reintroduce logic to pinpoint the problem.
- **Compatibility or graph creation errors**: Errors mentioning inability to create the loop system or missing graph support indicate that the runtime is too old or lacks required functionality. Upgrade the Salt execution engine to a version that supports Loop Open and Loop Close.
- **State not preserved between iterations**: If data or aux values reset each iteration, ensure Loop Open outputs are wired through your loop-body nodes back into the corresponding data/aux inputs on Loop Close, and that subsequent loop iterations consume the loop outputs instead of constant literals.
- **Condition evaluation errors**: Logged messages about condition evaluation failures followed by early loop termination usually mean the condition string is invalid or references missing variables. Check the syntax, ensure variables exist in LOOP or aux/data, and guard key lookups with membership checks where needed.
