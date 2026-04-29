# JSON Loop Open

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

JSON Loop Open iterates over a JSON array and outputs the current list item along with loop metadata and auxiliary data channels. It parses and validates the JSON input, determines the current index (optionally overridden), and evaluates a condition expression to decide whether the loop should continue or be marked finished. It is designed to be paired with JSON Loop Close for robust, list-driven iteration.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltjsonsaltloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use JSON Loop Open when you want to apply the same workflow segment to every element of a JSON array, such as processing a batch of user objects, prompts, or configuration entries. Place JSON Loop Open at the start of the looped section: set `json_list` to a valid JSON array, connect its `LOOP` output to the `LOOP` input of `SaltJsonSaltLoopClose`, and build your processing nodes between these two. Inside the loop body, use `current_item` as the primary per-element input, and route `aux`, `aux2`, `aux3`, and `aux4` through the loop (Open → body → Close → back to Open) to accumulate state such as lists of processed IDs, statistics, or logs. Leave `condition` as "True" for a simple full pass, or use it to stop early based on `current_item`, `current_index`, or your aux data. This node is typically upstream of JSON Loop Close and any per-item processing nodes, and it should not be mixed with generic loop open/close nodes in the same loop.

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
<tr><td style="word-wrap: break-word;">json_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON array that defines the elements to iterate over. The string must parse as valid JSON and the top-level value must be a list; otherwise the node blocks the loop and no iterations execute.</td><td style="word-wrap: break-word;">[{"id": 1, "prompt": "Describe a sunset"}, {"id": 2, "prompt": "Describe a forest"}]</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Boolean expression used to decide whether the loop continues on a given iteration. It is evaluated with access to variables like current_item, current_index, total_items, and loop metadata, plus any aux values. If blank or non-string, it defaults to "True". Evaluation errors cause the iteration to be blocked.</td><td style="word-wrap: break-word;">current_index < total_items and ("skip" not in current_item)</td></tr>
<tr><td style="word-wrap: break-word;">index_override</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Manually overrides the current index into the JSON list. The value is coerced to an integer; negative values are clamped to 0. Use this to jump to a particular item or implement custom indexing logic. If missing or invalid, iteration starts at 0.</td><td style="word-wrap: break-word;">2</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Primary auxiliary data channel carried across iterations. Typically used to accumulate results or maintain running context, such as a list of processed items or counters. Any type is allowed.</td><td style="word-wrap: break-word;">{"processed": [], "errors": []}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Secondary auxiliary data channel, independent of aux. Use this when your loop needs multiple separate pieces of mutable state.</td><td style="word-wrap: break-word;">["Started batch job", "Completed first 5 prompts"]</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Third auxiliary data channel for additional loop-level state or metrics.</td><td style="word-wrap: break-word;">{"max_runtime_ms": 750, "slow_item_id": 2}</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Fourth auxiliary data channel for any extra state you need to carry through the loop.</td><td style="word-wrap: break-word;">{"api_calls_made": 30}</td></tr>
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
<tr><td style="word-wrap: break-word;">LOOP</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Loop status object describing the JSON loop. Typically contains fields like id, items (the full array), index (current index), finished (boolean), total_items, and last_id. This must be connected to SaltJsonSaltLoopClose.LOOP for the loop to function.</td><td style="word-wrap: break-word;">{"id": 101, "items": [{"id": 1}, {"id": 2}], "index": 0, "finished": false, "total_items": 2, "last_id": 17}</td></tr>
<tr><td style="word-wrap: break-word;">current_item</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">The element of the JSON list at the current index, used as the main per-iteration payload in the loop body.</td><td style="word-wrap: break-word;">{"id": 1, "prompt": "Describe a sunset"}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Current value of the primary auxiliary data channel for this iteration. Wire this from JSON Loop Close back into JSON Loop Open to persist state.</td><td style="word-wrap: break-word;">{"processed": [1], "errors": []}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Current value of the secondary auxiliary channel for this iteration.</td><td style="word-wrap: break-word;">["Processed item 1 successfully"]</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Current value of the third auxiliary channel.</td><td style="word-wrap: break-word;">{"max_runtime_ms": 320}</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Current value of the fourth auxiliary channel.</td><td style="word-wrap: break-word;">{"notifications_sent": 2}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The entire json_list is parsed and held in memory. For very large arrays, consider splitting data into multiple smaller batches to avoid excessive memory use and long-running loops.
- **Limitations**: json_list must be valid JSON with a list at the top level. If parsing fails or the top-level value is not a list, the node blocks loop execution, and downstream loop body nodes will not run.
- **Behavior**: When the index reaches or exceeds the list length, or when the condition expression evaluates to false, the node marks the loop as finished and only outputs an updated LOOP state object while blocking all data outputs so JSON Loop Close can complete the loop.
- **Behavior**: The condition string is evaluated in a safe context that includes current_item, current_index, total_items, and loop status fields, plus aux values. Errors in the expression (such as referencing missing keys) result in the iteration being blocked.
- **Behavior**: index_override is cast to an integer, with negative values clamped to 0. Misconfiguration can cause the loop to skip items or finish earlier than expected.
- **Integration**: JSON Loop Open is designed specifically to work with SaltJsonSaltLoopClose; mixing it with generic loop closing nodes is not supported and will lead to incorrect behavior.

## Troubleshooting
- **Loop never runs**: If no items are processed and logs show JSON parsing errors or messages about non-list input, verify that json_list is a valid JSON array string using double quotes and square brackets (for example, `[{"id": 1}]`).
- **Loop finishes immediately**: If the loop ends after zero or one iterations, temporarily set condition to "True" and ensure index_override is not pointing past the last index. Also check that your condition does not reference fields that some items lack.
- **Condition evaluation errors**: If you see condition evaluation errors and blocked iterations, simplify the condition expression and ensure you only use available variables such as current_item, current_index, total_items, and defined aux keys.
- **Missing accumulated data**: If FINISHED? from JSON Loop Close is true but your aux-based accumulators (for example, processed IDs) are empty or unchanged, confirm that aux (and aux2–aux4, if used) are wired consistently from JSON Loop Open, through your processing nodes, into JSON Loop Close, and then from JSON Loop Close back into JSON Loop Open.
