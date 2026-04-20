# JSON Loop Open

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

JSON Loop Open iterates over a JSON array and emits the current item alongside loop status and auxiliary state. It parses the JSON, manages the loop index and finished flag, and evaluates a condition expression to decide whether to continue or finalize the loop. This node is intended to be paired with JSON Loop Close to build safe, controllable iteration over structured JSON inputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltjsonsaltloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use JSON Loop Open when you need to execute the same logic for every element in a JSON list, such as processing a list of prompts, URLs, configuration objects, or tasks. Place this node at the beginning of a loop construct: provide a JSON array string via json_list, optionally control execution with condition, and connect its LOOP output to the LOOP input of a SaltJsonSaltLoopClose node. Inside the loop body, route current_item into your processing nodes, and use aux, aux2, aux3, and aux4 to carry accumulators or intermediate results that must persist across iterations and be available when the loop finishes. Upstream nodes typically construct or fetch the JSON array (for example, from API, database, or text-building nodes). Downstream, combine this with SaltJsonSaltLoopClose and nodes that update the aux values. Ensure json_list is a valid JSON array, keep condition expressions simple and deterministic, and avoid extremely large arrays without batching or explicit iteration limits.

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
<tr><td style="word-wrap: break-word;">json_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array to iterate over. Must be valid JSON that decodes to a list (for example, ["a", "b"] or [{"id":1}, {"id":2}]). If parsing fails or the decoded value is not a list, the node blocks its data outputs and the loop body will not execute.</td><td style="word-wrap: break-word;">[{"image_url":"https://example.com/img1.png","caption":"A beach"},{"image_url":"https://example.com/img2.png","caption":"A mountain"}]</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Boolean expression evaluated each iteration to decide if the loop should proceed. Evaluated in a restricted environment; may reference current_item, current_index, aux, aux2, aux3, aux4, and loop status fields. If not a non-empty string, it defaults to "True". If evaluation throws an error or returns False, the loop is treated as finished and only LOOP state is emitted while data outputs are blocked.</td><td style="word-wrap: break-word;">current_index < 10 and (not current_item.get('skip', False))</td></tr>
<tr><td style="word-wrap: break-word;">index_override</td><td>False</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Optional manual override for the current loop index. Converted to an integer and used as the index into the JSON array (negative values are coerced to 0). Useful for resuming a loop from a particular position or implementing custom seeking behavior.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Primary auxiliary data carried through each iteration. Can be any type, such as a dictionary accumulator, running summary, or partial aggregate that your loop body updates and that you later retrieve via the Close node.</td><td style="word-wrap: break-word;">{"processed_count":3,"failed_items":[]}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Secondary auxiliary slot for additional loop-carried state, separate from aux. Use this to track an independent metric, list, or context object across iterations.</td><td style="word-wrap: break-word;">["embedding_chunk_1","embedding_chunk_2"]</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Third auxiliary slot for further state that should flow through the loop. Functionally identical to aux and aux2 but kept separate to avoid mixing unrelated data.</td><td style="word-wrap: break-word;">{"total_tokens":12000}</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Fourth auxiliary slot, same semantics as the other aux fields. Helpful in complex workflows that maintain multiple accumulators or context objects across iterations.</td><td style="word-wrap: break-word;">{"last_response":{"status":"ok","latency_ms":230}}</td></tr>
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
<tr><td style="word-wrap: break-word;">LOOP</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Loop status object for the current iteration. Must be connected to the LOOP input of a SaltJsonSaltLoopClose node. Contains fields such as id, items, index, finished, total_items, and last_id so the Close node can decide whether to continue or finalize the loop.</td><td style="word-wrap: break-word;">{"id":101,"items":[{"id":1},{"id":2}],"index":0,"finished":false,"total_items":2,"last_id":101}</td></tr>
<tr><td style="word-wrap: break-word;">current_item</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">The current element from the JSON array for this iteration. Its type matches the array contents (for example, string, number, or object). This is the main payload to process within the loop body.</td><td style="word-wrap: break-word;">{"image_url":"https://example.com/img1.png","caption":"A beach"}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Current value of the aux state for this iteration. Usually updated by downstream nodes and then re-entered into the loop via the Close node so that a final accumulated value is available when the loop completes.</td><td style="word-wrap: break-word;">{"processed_count":4,"failed_items":[2]}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Current value of the aux2 state for this iteration, passed through from the corresponding input. Use this for a second independent accumulator or context object.</td><td style="word-wrap: break-word;">["embedding_chunk_1","embedding_chunk_2","embedding_chunk_3"]</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Current value of the aux3 state for this iteration. Often used to track numeric metrics or structured metadata such as token counts or progress summaries.</td><td style="word-wrap: break-word;">{"total_tokens":18000,"avg_tokens_per_item":300}</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">any_type</td><td style="word-wrap: break-word;">Current value of the aux4 state for this iteration. Commonly used to carry the most recent API response, status information, or other per-iteration artifacts.</td><td style="word-wrap: break-word;">{"last_response":{"status":"ok","latency_ms":190}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Iterating over large JSON arrays or doing heavy per-item processing can be slow and resource intensive; consider batching the list or limiting iterations via the condition expression for very large inputs.
- **Limitations**: If json_list is not valid JSON or does not decode to a list, the node logs an error and blocks all data outputs for that pass; no items are processed and downstream loop body nodes will not run.
- **Behavior**: When the index reaches or exceeds the array length, or the condition evaluates to False, the node marks the loop as finished and only emits LOOP state with finished set to true while blocking current_item and aux outputs; the JSON Loop Close node then finalizes the loop.
- **Behavior**: Condition expressions are evaluated in a restricted environment; runtime errors in the expression cause the node to treat the loop as invalid for that pass and block outputs, so keep expressions simple and reference only documented variables.

## Troubleshooting
- **Invalid JSON list**: If the loop body never runs and logs show messages like "Error parsing JSON list" or "Input is not a JSON list", inspect json_list and confirm it is a syntactically correct JSON array string, not a single object or unquoted text.
- **Condition always false**: If the Close node reports the loop as finished immediately and no iterations are observed, check that the condition is not always False on the first iteration (for example, current_index > 0) and that index_override is not beyond the end of the list.
- **Condition evaluation error**: If logs include "Condition evaluation error" and the loop halts unexpectedly, simplify the condition, verify its syntax, and ensure it only references valid variables like current_item and current_index rather than undefined names.
- **Unexpected index or skipped items**: If items seem to be skipped or repeated, review any use of index_override and ensure external logic is not unintentionally jumping the index or reusing stale values between runs.
