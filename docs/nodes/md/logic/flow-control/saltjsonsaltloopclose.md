# JSON Loop Close

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Completes iterations of a JSON-based loop paired with JSON Loop Open. It evaluates a condition to decide whether to continue iterating or to finish and emit the final auxiliary values. When the loop is done (end of list or condition evaluates to false), it outputs FINISHED? = True along with the last aux states.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltjsonsaltloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node together with JSON Loop Open to iterate over a JSON array. Connect the LOOP output from JSON Loop Open to this node's LOOP input. Feed any auxiliary state (aux, aux2–aux4) back into this node to preserve and update it across iterations. The Close node advances the index and re-triggers the next iteration until the list is exhausted or the condition evaluates to false.

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
<tr><td style="word-wrap: break-word;">LOOP</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop state dictionary from JSON Loop Open. Required to continue or finalize the loop.</td><td style="word-wrap: break-word;"><LOOP from JSON Loop Open></td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Expression evaluated each cycle to decide whether to continue. Receives loop context including current_index, total_items, and any aux values.</td><td style="word-wrap: break-word;">current_index < total_items and (aux is None or aux.get('continue', True))</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Primary auxiliary data passed through each iteration. Use to carry and update state.</td><td style="word-wrap: break-word;">{"count": 5}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Additional auxiliary data slot.</td><td style="word-wrap: break-word;">["processed", "skipped"]</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Additional auxiliary data slot.</td><td style="word-wrap: break-word;">123</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Additional auxiliary data slot.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">FINISHED?</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True when the loop has finished (end of list reached or condition evaluated to false).</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the primary auxiliary data after the loop completes.</td><td style="word-wrap: break-word;">{"count": 42}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of aux2 after the loop completes.</td><td style="word-wrap: break-word;">["item1", "item3"]</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of aux3 after the loop completes.</td><td style="word-wrap: break-word;">987</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of aux4 after the loop completes.</td><td style="word-wrap: break-word;">false</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Must be paired with JSON Loop Open**: Always connect the LOOP output from JSON Loop Open to this node's LOOP input.
- **No current item output**: This node does not output the current JSON item; use JSON Loop Open's current_item for per-item processing.
- **Condition context**: The condition can reference loop variables like current_index, total_items, and any aux values.
- **Iteration advance**: On continue, the loop index automatically increments by 1 for the next item.
- **Finalization behavior**: If the condition is invalid or evaluates to false, the node finalizes and outputs FINISHED? = True with the current aux values.
- **State persistence**: To carry state across iterations, wire aux (and aux2–aux4 if needed) from JSON Loop Open through your processing chain back into this node.

## Troubleshooting
- **Loop never ends**: Ensure your condition eventually becomes false or that the JSON list is finite. Verify that aux state updates do not keep the condition permanently true.
- **FINISHED? never becomes True**: Confirm the LOOP input is connected from JSON Loop Open and that the condition is a valid string expression.
- **Aux values are None at the end**: Make sure aux/aux2–aux4 are connected through your processing nodes back into this node every iteration.
- **Condition errors**: If the condition has syntax or name errors, the loop will finalize. Use only variables available in the loop context (e.g., current_index, total_items, aux).
- **Empty JSON list**: An empty list finalizes immediately; FINISHED? will be True and aux outputs return their last provided values (possibly None).
