# JSON Loop Close

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Completes and controls iteration for a JSON-based loop started by JSON Loop Open. It evaluates a condition each pass, advances to the next item in the JSON list, preserves auxiliary values across iterations, and outputs a FINISHED? flag when the loop ends or the condition fails.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltjsonsaltloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node together with JSON Loop Open to iterate over a JSON array. Connect the LOOP output from JSON Loop Open to this node's LOOP input. Wire any auxiliary state (aux, aux2–aux4) you want to preserve across iterations into both nodes. Optionally set a condition (as a string expression) to determine whether the loop should continue. When the loop completes (end of list or condition evaluates to False), FINISHED? outputs True and the latest auxiliary values are returned.

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
<tr><td style="word-wrap: break-word;">LOOP</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop status dictionary from JSON Loop Open. Required to continue or finalize the loop.</td><td style="word-wrap: break-word;">Connect from JSON Loop Open → LOOP</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">String expression evaluated each pass to decide whether to continue iterating. Uses loop variables from LOOP and any provided aux values.</td><td style="word-wrap: break-word;">index + 1 < total_items</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Primary auxiliary data passed through each iteration and returned when finished.</td><td style="word-wrap: break-word;">{"accumulator": []}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Additional auxiliary data channel preserved across iterations.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Additional auxiliary data channel preserved across iterations.</td><td style="word-wrap: break-word;">["status", "ok"]</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Additional auxiliary data channel preserved across iterations.</td><td style="word-wrap: break-word;">null</td></tr>
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
<tr><td style="word-wrap: break-word;">FINISHED?</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True when the loop has completed (end of list or condition evaluated to False).</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the primary auxiliary data after the loop finishes.</td><td style="word-wrap: break-word;">{"accumulator": [1,2,3]}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the second auxiliary channel.</td><td style="word-wrap: break-word;">84</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the third auxiliary channel.</td><td style="word-wrap: break-word;">["status","done"]</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the fourth auxiliary channel.</td><td style="word-wrap: break-word;">null</td></tr>
</tbody>
</table>
</div>

## Important Notes
- You must connect the LOOP output of JSON Loop Open to this node’s LOOP input; otherwise the loop cannot proceed.
- Condition is a string expression evaluated safely using loop variables from LOOP (e.g., index, total_items). If invalid or empty, it is treated as True by default on Open and evaluated on Close; an error on Close finalizes the loop.
- This node advances the JSON index by 1 each iteration and preserves aux, aux2, aux3, and aux4 across passes.
- On finalization (end of list or condition False), data outputs return the latest auxiliary values and FINISHED? = True.
- Close node conditions cannot directly access current_item unless you store it in aux on the Open side or otherwise pass it along.

## Troubleshooting
- FINISHED? never becomes True: Ensure the condition eventually evaluates to False or that the list is not empty. Verify that the index is advancing (do not override index in Open each pass unless intended).
- Condition errors immediately finalize the loop: Check the condition syntax and available variables (e.g., use 'index' and 'total_items' from LOOP).
- Aux data not preserved between iterations: Make sure aux (and aux2–aux4) outputs from Open are connected to the matching aux inputs of Close, and vice versa, so values are forwarded each pass.
- Loop does not start or stalls: Confirm that JSON Loop Open is receiving a valid JSON array and that its LOOP output is connected. If Open blocked due to invalid JSON, fix the json_list input.
