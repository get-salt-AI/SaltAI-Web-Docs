# JSON Loop Open

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Begins a loop over a JSON array and emits the current item and auxiliary values for each iteration. It manages loop state in a LOOP dictionary, evaluates a per-iteration condition, and coordinates with JSON Loop Close to advance or finalize the loop.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltjsonsaltloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to iterate through a JSON array one item at a time. Provide the JSON array (as a string) and an optional condition expression. Connect the LOOP output to a JSON Loop Close node, and use current_item in downstream processing. The Close node will either trigger the next iteration (by incrementing index) or finalize the loop when done or when the condition fails.

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
<tr><td style="word-wrap: break-word;">json_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON array to iterate over. Must parse into a list (e.g., ["a", "b"], [1,2,3], or an array of objects).</td><td style="word-wrap: break-word;">["item1", "item2", "item3"]</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Boolean expression evaluated each iteration to determine if processing should proceed. If false, the loop state is returned and data outputs are blocked for that pass.</td><td style="word-wrap: break-word;">current_index < 10 and (current_item.get('active', True) if isinstance(current_item, dict) else True)</td></tr>
<tr><td style="word-wrap: break-word;">index_override</td><td>False</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Manually sets the zero-based index for the current iteration. Typically driven by the Close node to advance the loop.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Auxiliary data passed through each iteration. Useful for carrying state across loop steps.</td><td style="word-wrap: break-word;">{"accumulator": 5}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Second auxiliary data slot passed through each iteration.</td><td style="word-wrap: break-word;">[]</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Third auxiliary data slot passed through each iteration.</td><td style="word-wrap: break-word;">"context string"</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Fourth auxiliary data slot passed through each iteration.</td><td style="word-wrap: break-word;">42</td></tr>
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
<tr><td style="word-wrap: break-word;">LOOP</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Dictionary describing loop state. Connect this to JSON Loop Close. Contains fields like id, items, index, finished, total_items, and last_id.</td><td style="word-wrap: break-word;">{"id": 12345, "items": [1,2,3], "index": 0, "finished": false, "total_items": 3, "last_id": "<unique-id>"}</td></tr>
<tr><td style="word-wrap: break-word;">current_item</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">The current item from the JSON array at the active index.</td><td style="word-wrap: break-word;">{"name": "item1", "active": true}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Pass-through auxiliary data for use and mutation within the loop.</td><td style="word-wrap: break-word;">{"accumulator": 5}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Second auxiliary pass-through value.</td><td style="word-wrap: break-word;">[]</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Third auxiliary pass-through value.</td><td style="word-wrap: break-word;">"context string"</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Fourth auxiliary pass-through value.</td><td style="word-wrap: break-word;">42</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Condition variables**: The condition expression can reference current_item, current_index, and fields from the LOOP dictionary (e.g., index, total_items). Global and auxiliary variables provided as inputs are also available.
- **End-of-list behavior**: When index reaches or exceeds the number of items, finished is set to True and data outputs are blocked for that pass so the Close node can finalize cleanly.
- **Condition failure**: If the condition evaluates to False or throws an error, data outputs are blocked for that pass and the Close node will finalize the loop.
- **Indexing**: index_override is sanitized to an integer >= 0. If omitted, the Close node typically sets it to advance the loop.
- **JSON parsing**: json_list must be a valid JSON array. Non-array JSON (e.g., an object) causes the node to block outputs for the pass.
- **Auxiliary data**: Up to four auxiliary values are carried through iterations and returned by the Close node upon completion.

## Troubleshooting
- **Invalid JSON**: If json_list is not valid JSON or not an array, the node will log an error and block outputs. Provide a valid JSON array string (e.g., [1,2,3]).
- **Condition errors**: Syntax or runtime errors in condition expressions cause the node to block. Simplify the condition and ensure referenced variables (e.g., current_item, current_index) exist.
- **No progress between iterations**: Ensure the LOOP output is connected to JSON Loop Close and that Close is configured to advance index (it automatically sets index_override for the next iteration).
- **Index out of range**: If index_override points beyond the last element, the loop finalizes immediately. Use a valid index within 0..len(items)-1.
- **Unexpected None outputs**: When the condition fails or end-of-list is reached, data outputs are intentionally blocked for that pass. This is expected—use the Close node’s FINISHED? output to detect completion.
