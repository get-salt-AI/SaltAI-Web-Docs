# JSON Loop Open

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Starts a loop over a JSON array and emits the current item for each iteration. It maintains loop state in a LOOP object and passes through up to four auxiliary values so they can be preserved or updated across iterations.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltjsonsaltloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to iterate over a JSON array and process each item in sequence. Connect its LOOP output to a JSON Loop Close node to drive iteration. Provide a JSON array in json_list, optionally gate iteration with a boolean condition, and use aux channels to carry state between iterations. The loop advances when the Close node triggers the next iteration.

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
<tr><td style="word-wrap: break-word;">json_list</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The JSON array to iterate over. Accepts a JSON string representing an array or a pre-parsed list provided by an upstream node.</td><td style="word-wrap: break-word;">[{"id":1,"active":true},{"id":2,"active":false}]</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A boolean expression (as text) that controls whether the current iteration should proceed. If it evaluates to false, the loop will finalize on the Close node. Useful variables include: current_item, current_index, total_items.</td><td style="word-wrap: break-word;">current_index < total_items and (current_item.get('active', False) == True)</td></tr>
<tr><td style="word-wrap: break-word;">index_override</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Manually sets the current index for the loop (0-based). Use to resume or jump within the JSON array.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Primary auxiliary value to pass through each iteration (e.g., an accumulator or context object).</td><td style="word-wrap: break-word;">{"sum": 0}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Secondary auxiliary value passed through each iteration.</td><td style="word-wrap: break-word;">["log entry 1"]</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Tertiary auxiliary value passed through each iteration.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Quaternary auxiliary value passed through each iteration.</td><td style="word-wrap: break-word;">42</td></tr>
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
<tr><td style="word-wrap: break-word;">LOOP</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Loop state dictionary containing information like id, index, total_items, finished, and internal linkage. Connect this to the LOOP input of a JSON Loop Close node.</td><td style="word-wrap: break-word;">{"id": "<loop-id>", "index": 0, "total_items": 3, "finished": false}</td></tr>
<tr><td style="word-wrap: break-word;">current_item</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">The current element from the JSON array for this iteration.</td><td style="word-wrap: break-word;">{"id":1,"active":true}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Passthrough of the aux value for this iteration.</td><td style="word-wrap: break-word;">{"sum": 10}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Passthrough of the aux2 value for this iteration.</td><td style="word-wrap: break-word;">["log entry 1","log entry 2"]</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Passthrough of the aux3 value for this iteration.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Passthrough of the aux4 value for this iteration.</td><td style="word-wrap: break-word;">99</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Condition defaults to True**: If left empty or non-boolean, it is treated as True. Provide a valid boolean expression as text.
- **End-of-list or failed condition**: When the index reaches the end of the list or the condition evaluates to false, only the LOOP state is updated for finalization and the JSON Loop Close node will output FINISHED? = True.
- **Auxiliary passthrough**: Up to four aux values (aux, aux2, aux3, aux4) are carried across iterations; connect them to the Close node to preserve/update state.
- **Indexing**: index_override is 0-based. Negative or invalid values are sanitized to 0.
- **Input format**: json_list should represent an array (e.g., [1,2,3]). Objects or invalid JSON will not iterate.
- **Pairing required**: This node must be paired with JSON Loop Close to advance iterations and detect completion.

## Troubleshooting
- **Invalid JSON in json_list**: Ensure json_list is a valid JSON array string (e.g., "[1,2,3]") or provide a list from an upstream node.
- **Condition errors**: If the condition references missing variables or is not a valid boolean expression, iteration will not proceed. Use variables like current_item, current_index, and total_items.
- **Loop never finishes**: Verify the LOOP output is connected to a JSON Loop Close node and that the condition will eventually become false or the index reaches the end.
- **No current_item output**: This is expected when the loop is finalizing (end of list or condition false). Check the Close node for FINISHED? status.
- **Unexpected index**: If iteration restarts at 0, ensure index_override is an integer and is being set correctly from the Close node when continuing.
