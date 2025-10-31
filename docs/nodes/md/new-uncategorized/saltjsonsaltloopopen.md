# JSON Loop Open

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Starts a loop over a JSON array and exposes the current item and a loop-state object. It evaluates an optional condition each iteration to determine whether to proceed, and passes through up to four auxiliary values that can be maintained across iterations.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltjsonsaltloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to iterate over items in a JSON array. Provide the JSON in json_list and optionally a condition expression to gate execution per iteration. Connect the LOOP output to a JSON Loop Close node to advance the loop. Use current_item to process the current element, and aux/aux2/aux3/aux4 to carry state or additional data across iterations.

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
<tr><td style="word-wrap: break-word;">json_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON array to iterate over. Can be a JSON-encoded string (e.g., "[1, 2, 3]") or a list provided from an upstream node.</td><td style="word-wrap: break-word;">["alpha", "beta", "gamma"]</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">An expression evaluated each iteration to decide whether to proceed. You can reference loop variables like current_item, current_index, items, total_items, finished, and any provided aux values.</td><td style="word-wrap: break-word;">current_index < total_items and (aux is None or aux.get('enabled', True))</td></tr>
<tr><td style="word-wrap: break-word;">index_override</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Overrides the current index for the iteration (0-based). Useful to start from a specific position or to resume.</td><td style="word-wrap: break-word;">2</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Primary auxiliary value passed through each iteration. Use it to carry state or context.</td><td style="word-wrap: break-word;">{'running_sum': 15, 'enabled': True}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Second auxiliary value passed through the loop.</td><td style="word-wrap: break-word;">['tag1', 'tag2']</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Third auxiliary value passed through the loop.</td><td style="word-wrap: break-word;">session-123</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Fourth auxiliary value passed through the loop.</td><td style="word-wrap: break-word;">0.75</td></tr>
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
<tr><td style="word-wrap: break-word;">LOOP</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Loop-state object containing metadata for the iteration. Connect this to the LOOP input of a JSON Loop Close node to continue iterating.</td><td style="word-wrap: break-word;">{'id': 102938, 'items': ['alpha', 'beta', 'gamma'], 'index': 1, 'finished': False, 'total_items': 3, 'last_id': 'node-uuid'}</td></tr>
<tr><td style="word-wrap: break-word;">current_item</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">The current element from the JSON array for this iteration.</td><td style="word-wrap: break-word;">beta</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Pass-through auxiliary value for state sharing across iterations.</td><td style="word-wrap: break-word;">{'running_sum': 23}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Second pass-through auxiliary value.</td><td style="word-wrap: break-word;">['processed']</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Third pass-through auxiliary value.</td><td style="word-wrap: break-word;">session-123</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Fourth pass-through auxiliary value.</td><td style="word-wrap: break-word;">0.9</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Condition variables**: The condition expression can reference current_item, current_index, items, index, total_items, finished, and any provided aux/aux2/aux3/aux4 values.
- **Pairing required**: To advance the loop, connect the LOOP output to a JSON Loop Close node; that node triggers the next iteration.
- **Indexing**: index_override is 0-based. If it is beyond the array length, the loop is considered finished.
- **Aux slots**: Up to four auxiliary slots (aux through aux4) are available for carrying data/state across iterations.
- **Input format**: json_list must represent an array. Non-array JSON (e.g., an object) will not iterate as expected.

## Troubleshooting
- **Loop never starts**: Ensure condition evaluates to True on the first iteration and that json_list is a valid array.
- **Invalid JSON**: If json_list is a malformed JSON string, correct the format or provide a list from an upstream node.
- **Immediate finish**: If index_override is greater than or equal to the number of items, the loop will be marked finished and current_item will be None.
- **No progression**: Verify the LOOP output is connected to a JSON Loop Close node; without it, subsequent iterations won't execute.
- **Aux not updating**: Confirm aux (and other aux slots) are wired through both Open and Close nodes and updated by the nodes in between as needed.
