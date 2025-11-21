# Loop Close

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Terminates or continues a loop started by a Loop Open node. It evaluates a condition and either signals completion with final data or schedules the next iteration while preserving and forwarding loop state and data across iterations.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node together with Loop Open to build iterative sections in your workflow. Connect the LOOP output of Loop Open to the LOOP input here. Feed any evolving state into data and aux inputs, and wire the corresponding outputs from Loop Open back into Loop Close to carry state across iterations. The condition input controls whether the loop continues; when it evaluates to False or the loop is finished, FINISHED? becomes True and the node emits the final data values.

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
<tr><td style="word-wrap: break-word;">LOOP</td><td>True</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Loop context dictionary coming from Loop Open. It contains loop state such as id, index, step, start, end, finished, and other metadata needed to manage iterations.</td><td style="word-wrap: break-word;">{'id': 12345, 'index': 3, 'step': 1, 'start': 0, 'end': 5, 'finished': False}</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Expression that must evaluate to True for the loop to continue. You can reference loop variables from LOOP (e.g., index, step, start, end, finished) and any data you pass into this node.</td><td style="word-wrap: break-word;">index < 10 and not finished</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Primary data payload to carry and update each iteration. Connect the matching output from Loop Open to preserve and evolve this value.</td><td style="word-wrap: break-word;">{'count': 7, 'items': ['a', 'b', 'c']}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary data to carry alongside the main data each iteration.</td><td style="word-wrap: break-word;">0.85</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Additional auxiliary slot for iterative state.</td><td style="word-wrap: break-word;">running_total</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Additional auxiliary slot for iterative state.</td><td style="word-wrap: break-word;">[1, 2, 3]</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Additional auxiliary slot for iterative state.</td><td style="word-wrap: break-word;">{'threshold': 0.2}</td></tr>
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
<tr><td style="word-wrap: break-word;">FINISHED?</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True when the loop has completed (either because the condition evaluated to False or the loop reached its configured end).</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final value of the primary data after the last iteration.</td><td style="word-wrap: break-word;">{'count': 10, 'items': ['a', 'b', 'c', 'd']}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final value of the auxiliary data after the last iteration.</td><td style="word-wrap: break-word;">0.97</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final value of the aux2 slot after the last iteration.</td><td style="word-wrap: break-word;">done</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final value of the aux3 slot after the last iteration.</td><td style="word-wrap: break-word;">[1, 2, 3, 4]</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final value of the aux4 slot after the last iteration.</td><td style="word-wrap: break-word;">{'threshold': 0.2, 'met': True}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Connect LOOP properly**: The LOOP input must come from the LOOP output of a Loop Open node or the loop cannot execute.
- **Condition controls continuation**: If condition is empty it is treated as True. When it evaluates to False, the loop completes and FINISHED? becomes True.
- **State preservation**: To maintain and update values across iterations, wire matching outputs from Loop Open back into the corresponding inputs on Loop Close.
- **Multiple aux slots**: Up to four auxiliary slots (aux through aux4) allow carrying multiple pieces of state through the loop.
- **Updated runtime required**: Loops rely on advanced execution features; ensure your environment is up-to-date to avoid execution blocker behavior.
- **Finalization behavior**: When the loop completes, this node outputs FINISHED? = True with the last known values for data and aux slots.

## Troubleshooting
- **FINISHED? never becomes True**: Verify the condition will eventually evaluate to False or that your loop configuration in Loop Open allows reaching an end state.
- **Node doesn't iterate**: Ensure the LOOP input is connected from Loop Open and that your condition evaluates to True on the first pass.
- **Data resets to None each iteration**: Confirm the corresponding outputs from Loop Open are wired back into the inputs of Loop Close so state is preserved.
- **Runtime error or blocked execution**: Update your environment to the latest version; loop nodes require modern execution capabilities.
- **Condition errors**: Use a valid expression and available variables (e.g., index, step, start, end, finished). Test with a simple expression like "True" to isolate issues.
- **Unexpected infinite loop**: Add an exit condition (e.g., index >= 10) or configure bounds on the Loop Open side to guarantee termination.
