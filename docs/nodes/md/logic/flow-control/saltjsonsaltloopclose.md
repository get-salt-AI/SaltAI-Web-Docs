# JSON Loop Close

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

The JSON Loop Close node works together with JSON Loop Open to iterate over elements of a JSON array. On each call it evaluates a condition against the loop state and auxiliary data to decide whether to trigger another iteration or finalize the loop. When the loop finishes, it outputs FINISHED? = True and returns the final values of the auxiliary data streams carried through the loop.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltjsonsaltloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use JSON Loop Close whenever you are processing a JSON array item-by-item with JSON Loop Open and need controlled termination plus access to the final accumulated state. A typical workflow is: SaltJsonSaltLoopOpen reads a JSON list (such as user records, URLs, or job configs), exposes the current_item and aux values to a subgraph that performs work, then passes the loop status and aux values into JSON Loop Close. JSON Loop Close evaluates the condition expression using the loop dictionary (id, index, finished, total_items, last_id) plus any global logic variables and aux values. If the loop is marked finished or the condition evaluates to false, the node returns FINISHED? = True with the final aux, aux2, aux3, and aux4 values for downstream nodes (for example, result aggregators, reporting, or follow-up logic). If the loop is not finished and the condition is true, the node reconstructs and schedules the loop body subgraph for the next iteration. Best practice is to keep the condition expression simple and deterministic, use aux slots to accumulate any results or metrics you will need after the loop, and avoid passing very large objects through aux on every iteration to reduce memory and storage pressure.

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
<tr><td style="word-wrap: break-word;">LOOP</td><td>True</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Loop status object produced by SaltJsonSaltLoopOpen. Must be connected directly from the LOOP output of the JSON Loop Open node. It includes fields such as id, items, index, finished, total_items, and last_id, and is used by JSON Loop Close to decide whether to continue or finalize the loop.</td><td style="word-wrap: break-word;">{"id":42,"items":["user-1","user-2"],"index":1,"finished":false,"total_items":2,"last_id":1234}</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A boolean expression, provided as a string, that controls whether the loop should continue from the Close node. It is evaluated in a context that includes the LOOP dictionary (for example, index, finished, total_items), any global logic variables, and all aux inputs. If not a non-empty string, it defaults to "True". When this expression evaluates to false or raises an error, the loop is finalized even if there are remaining items.</td><td style="word-wrap: break-word;">current_index < total_items - 1 and error_count == 0</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Primary auxiliary data that flows through each loop iteration and back into JSON Loop Close. Use this to carry your main accumulated state (such as a growing list of processed results, or a dictionary of aggregated values) across iterations. On the final call it is returned as the aux output unchanged.</td><td style="word-wrap: break-word;">[{"user_id":"u1","status":"ok"},{"user_id":"u2","status":"ok"}]</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Second auxiliary channel for additional iterative state. Commonly used for metrics, error logs, or secondary aggregates while aux holds primary results. Also passed through and returned unchanged when the loop finishes.</td><td style="word-wrap: break-word;">{"error_count":0,"warnings":["missing optional field: phone"]}</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Third auxiliary channel identical in behavior to aux and aux2. Use this when you need to maintain a separate piece of state across iterations, such as raw latency samples or debug traces.</td><td style="word-wrap: break-word;">{"latencies_ms":[120,98,110]}</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Fourth auxiliary channel for additional loop-carried state. It behaves like the other aux inputs and is returned as-is after the final iteration.</td><td style="word-wrap: break-word;">{"retry_queue":["job-17","job-21"]}</td></tr>
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
<tr><td style="word-wrap: break-word;">FINISHED?</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Indicates whether the JSON loop has completed. Returns true when the LOOP state is marked finished or when the Close-side condition evaluates to false or errors; otherwise false, meaning another iteration will be scheduled by the engine.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Final value of the primary auxiliary data after the loop completes. Typically contains accumulated results such as a list of all processed items or a summary structure built up over all iterations.</td><td style="word-wrap: break-word;">[{"user_id":"u1","status":"ok"},{"user_id":"u2","status":"ok"},{"user_id":"u3","status":"ok"}]</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Final value of the second auxiliary data stream. Often used for error or warning summaries, counters, or secondary metrics derived during the loop.</td><td style="word-wrap: break-word;">{"error_count":1,"errors":["user u4: invalid email"]}</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Final value of the third auxiliary data stream at loop completion. Only non-null if aux3 was used in the loop body.</td><td style="word-wrap: break-word;">{"avg_latency_ms":105.7,"p95_latency_ms":140.3}</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Final value of the fourth auxiliary data stream once the loop is finished. Use this to expose any remaining loop-carried state to downstream consumers.</td><td style="word-wrap: break-word;">{"failed_job_ids":["job-21","job-34"]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Each additional iteration requires reconstructing and executing the loop body subgraph; loops with large bodies or many iterations can be expensive in time and memory, so keep per-iteration work as lightweight as possible.
- **Limitations**: The condition string is evaluated by a restricted expression evaluator; advanced Python features such as imports, arbitrary function calls, and assignments are not supported and will cause evaluation errors that finalize the loop.
- **Behavior**: If the LOOP object has finished set to true or the Close-side condition evaluates to false or throws an error, JSON Loop Close immediately returns FINISHED? = true and the current aux values without scheduling further iterations.
- **Behavior**: Auxiliary values (aux through aux4) are not automatically accumulated; they are simply passed through from the current iteration. Any aggregation logic, such as appending to lists or updating counts, must be performed inside the loop body before reaching this node.
- **Performance**: Carrying very large data structures in aux, such as huge lists or binary payloads, across many iterations can greatly increase workflow state size and slow checkpointing or resumption; prefer compact summary structures when possible.
- **Limitations**: The LOOP input must originate from a compatible SaltJsonSaltLoopOpen node; constructing or mutating the LOOP dictionary manually can break loop control and lead to unexpected failures.

## Troubleshooting
- **Condition evaluation error in Close**: If logs indicate a condition evaluation error and the loop stops early, the condition likely references missing variables or uses unsupported syntax. Simplify the condition and ensure all referenced names exist in the loop status or aux values.
- **Loop never finishes (FINISHED? stays false)**: This typically means the condition remains true and the open-side logic also continues the loop. Add an explicit termination clause based on index or total_items, such as current_index >= total_items - 1, to guarantee exit.
- **Unexpected early termination**: If FINISHED? becomes true sooner than expected, check both the LOOP.finished flag from JSON Loop Open and your Close-side condition. If the open node flags finished early or the condition evaluates to false due to missing data, the loop will finalize immediately.
- **Missing or invalid LOOP input**: If LOOP is disconnected, not coming from JSON Loop Open, or has been manually modified, the node may be unable to manage iterations correctly. Verify that LOOP is wired directly from SaltJsonSaltLoopOpen and avoid editing its contents.
- **Aux outputs are null on final iteration**: If aux or aux2 through aux4 are null when FINISHED? is true, they were never supplied into JSON Loop Close. Ensure your loop body consistently passes the desired state into the corresponding aux inputs each iteration.
