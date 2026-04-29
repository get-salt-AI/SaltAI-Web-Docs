# JSON Loop Close

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

JSON Loop Close works with JSON Loop Open to iterate over items in a JSON array. It receives loop state from the open node, evaluates a close condition, and either triggers another iteration by reconstructing the subgraph or finalizes the loop. When finalized, it outputs FINISHED? = True along with the final auxiliary values accumulated across iterations.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltjsonsaltloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use JSON Loop Close when you want to process each element of a JSON array while carrying state between iterations. Upstream, add a SaltJsonSaltLoopOpen node that takes a JSON list (for example, an array of tasks or records) and outputs a LOOP dictionary plus current_item and auxiliary values. Between JSON Loop Open and JSON Loop Close, build the subgraph that processes current_item and updates aux / aux2 / aux3 / aux4. Connect the LOOP output of SaltJsonSaltLoopOpen directly into the LOOP input of JSON Loop Close. On each execution, JSON Loop Close evaluates its condition against the LOOP state and provided variables: if the loop has not finished and the condition is true, it clones and reconnects the nodes between open and close and advances the index to the next JSON item; if the loop is finished or the condition is false or invalid, it stops scheduling new iterations and returns FINISHED? = True with the final aux values. Downstream, place aggregation or post-processing nodes that should run only once, gating them with FINISHED? so they activate after the entire JSON list has been processed.

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
<tr><td style="word-wrap: break-word;">LOOP</td><td>True</td><td style="word-wrap: break-word;">ANY (loop state dictionary)</td><td style="word-wrap: break-word;">Loop state dictionary produced by SaltJsonSaltLoopOpen. It must be connected from that node's LOOP output. The structure typically includes fields like `id`, `items`, `index`, `finished`, `total_items`, and `last_id`. JSON Loop Close uses this state to determine whether the loop has completed and to find the nodes between JSON Loop Open and JSON Loop Close that should be cloned for the next iteration.</td><td style="word-wrap: break-word;">{"id": 123456, "items": ["user-1", "user-2"], "index": 0, "finished": false, "total_items": 2, "last_id": 42}</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Expression string that is safely evaluated each time the loop closes to decide whether to continue. It has access to fields in the LOOP dictionary (e.g., `index`, `finished`, `total_items`) along with auxiliary variables added to the evaluation context. If it evaluates to false or throws an evaluation error, the loop finalizes and FINISHED? becomes true. If empty or not a string, it is treated as "True".</td><td style="word-wrap: break-word;">not finished and current_index < total_items</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Primary auxiliary data channel for carrying arbitrary state between iterations. Usually connected from aux outputs in the loop body back into this node. When the loop completes, this is returned as the aux output and often represents the main aggregated result (such as a list of per-item outputs or accumulated data).</td><td style="word-wrap: break-word;">[{"email":"user1@example.com","status":"sent"},{"email":"user2@example.com","status":"sent"}]</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Secondary auxiliary channel for additional iterative state, such as counters, metrics, or a second result collection. Its final value at loop completion is emitted on the aux2 output.</td><td style="word-wrap: break-word;">{"total_success":18,"total_failed":2}</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Third auxiliary channel, typically used for logs, timing data, or other diagnostic or secondary state that you want to persist through the loop and access after completion.</td><td style="word-wrap: break-word;">[{"index":0,"duration_ms":120},{"index":1,"duration_ms":95}]</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Fourth auxiliary channel for any extra state you want to preserve across iterations, such as error lists, retry queues, or miscellaneous aggregates. At the end of the loop, its value is provided as the aux4 output.</td><td style="word-wrap: break-word;">{"error_items":["user-7","user-19"],"retry_queue":["user-19"]}</td></tr>
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
<tr><td style="word-wrap: break-word;">FINISHED?</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True once the JSON loop has fully completed, either because all items were processed or because the close-node condition evaluated to false. Before completion, the node primarily drives further iterations instead of emitting final outputs.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final value of the primary auxiliary state at loop completion. This is often the main aggregated result, such as a list of generated outputs or a merged JSON structure built over all iterations.</td><td style="word-wrap: break-word;">[{"prompt":"item 1","response":"..."},{"prompt":"item 2","response":"..."}]</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final value of the second auxiliary state, typically containing metrics, counters, or alternative aggregates that were updated on each iteration.</td><td style="word-wrap: break-word;">{"tokens_used":24567,"items_processed":120}</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final value of the third auxiliary channel, often used for diagnostic logs or timing information accumulated during the loop.</td><td style="word-wrap: break-word;">["log: started batch","log: processed item 3","log: processed item 4"]</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final value of the fourth auxiliary channel, enabling complex workflows to expose multiple distinct result streams from the loop.</td><td style="word-wrap: break-word;">{"last_item_processed":"user-123","last_status":"skipped"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: JSON Loop Close and JSON Loop Open dynamically clone and reconnect the subgraph between them for each iteration; large or deep subgraphs combined with many iterations can significantly increase execution time and memory usage.
- **Performance**: Condition expressions that inspect large values (for example, big lists or dictionaries stored in aux) can slow each iteration; prefer conditions based on indexes, flags, or compact summaries where possible.
- **Limitations**: The LOOP input must be the loop state produced by a matching SaltJsonSaltLoopOpen node. Using arbitrary dictionaries or states from other loop node types is unsupported and can cause incorrect behavior.
- **Limitations**: The condition string is evaluated in a restricted, safe environment. Arbitrary Python code is not allowed; only supported operators, literals, and variables exposed from LOOP state and auxiliary values can be used.
- **Behavior**: When LOOP.finished is true or the evaluated condition is false, JSON Loop Close stops scheduling new iterations and emits its normal outputs (FINISHED? plus aux values) exactly once.
- **Behavior**: If the runtime environment lacks the required graph-building APIs, this node raises an error explaining that the JSON loop system cannot be created and that a newer engine version is required.

## Troubleshooting
- **Condition evaluation error in Close**: If logs show `Condition evaluation error in Close` and the loop ends immediately, the condition expression is invalid or references unknown variables. Simplify it (for example, `not finished`) and ensure that all variables exist in the LOOP state or aux values.
- **Loop never terminates**: If the loop appears to run indefinitely, verify that your condition will eventually evaluate to false or that you rely on the open-node `finished` flag (for example, use `not finished` instead of `True`). Also confirm that the JSON list length and index progression are as expected.
- **Immediate FINISHED? without iterations**: If FINISHED? is true on the first pass and no per-item processing occurs, check that SaltJsonSaltLoopOpen receives a valid JSON array (e.g., `["item1","item2"]`) and that its index is not overridden to a value at or beyond the array length at the start.
- **Aux outputs are None at the end**: If final aux / aux2 / aux3 / aux4 values are None, verify that these channels are wired correctly through your loop body, from JSON Loop Open outputs through processing nodes and back into JSON Loop Close inputs for every iteration.
- **Environment too old for JSON loops**: If you encounter an error about being unable to create a JSON Loop system or needing a newer engine version, update the Salt execution engine to a release that supports loop inversion and JSON Loop nodes before using this node.
