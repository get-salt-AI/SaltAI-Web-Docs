# Loop Open

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Initializes a loop and outputs a loop control object along with passthrough data slots. It supports for-style iteration using start/step/end and while-style behavior via an evaluable condition. The node exposes data and auxiliary channels that can be fed into a matching Loop Close node to persist and update values across iterations.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use Loop Open to repeat a section of your workflow. Connect its LOOP output to the LOOP input of a Loop Close node, and wire any data/aux outputs back into the corresponding inputs on Loop Close to carry state forward each iteration. Configure start, step, and end for fixed-length loops, add a condition for while-style loops, or combine both (the loop stops when the condition becomes false or the end is reached).

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
<tr><td style="word-wrap: break-word;">start</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Initial index value for the loop counter.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">step</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Increment (or decrement if negative) applied to the index each iteration. Set to 0 for pure condition-based loops.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">end</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Terminal value used to determine when the loop ends for for-style iteration. The loop is considered finished when the index reaches/passes this end according to the step direction.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Expression that must evaluate to True for the loop to proceed. Can reference loop variables (e.g., index, start, step, end) and any passed data/aux inputs.</td><td style="word-wrap: break-word;">index < end and (data is not None) or True</td></tr>
<tr><td style="word-wrap: break-word;">index_override</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Optional manual override of the current loop index. Use to jump to a specific iteration or perform custom step logic.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Primary data payload to iterate on. If connected through Loop Close, changes persist across iterations.</td><td style="word-wrap: break-word;">['item1', 'item2', 'item3']</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary data channel 1 for additional state.</td><td style="word-wrap: break-word;">{'counter': 0}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary data channel 2.</td><td style="word-wrap: break-word;">{'flag': True}</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary data channel 3.</td><td style="word-wrap: break-word;">context-string</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary data channel 4.</td><td style="word-wrap: break-word;">[1, 2, 3]</td></tr>
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
<tr><td style="word-wrap: break-word;">LOOP</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop control dictionary containing status for the current iteration (id, start, end, step, index, finished, and other context). Connect to the LOOP input of Loop Close.</td><td style="word-wrap: break-word;">{'id': '<unique-id>', 'index': 1, 'start': 1, 'step': 1, 'end': 10, 'finished': False}</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Passthrough of the data input to be iterated and updated across iterations.</td><td style="word-wrap: break-word;">['item1', 'item2', 'item3']</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Passthrough of auxiliary data channel 1.</td><td style="word-wrap: break-word;">{'counter': 0}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Passthrough of auxiliary data channel 2.</td><td style="word-wrap: break-word;">{'flag': True}</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Passthrough of auxiliary data channel 3.</td><td style="word-wrap: break-word;">context-string</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Passthrough of auxiliary data channel 4.</td><td style="word-wrap: break-word;">[1, 2, 3]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Use step > 0 to count up and step < 0 to count down. The loop is considered finished when the index crosses or reaches end according to step direction.
- Set step to 0 for pure while-style loops controlled solely by condition.
- You can combine for- and while-style behavior: the loop continues while condition is true and stops early if finished by reaching end.
- Wire the data and aux outputs of Loop Open into the matching inputs of Loop Close to persist changes across iterations.
- If condition evaluates to False at the start, the loop does not execute and downstream execution is blocked for this branch.
- The condition string is evaluated against a variable map that includes loop status (index, start, step, end, finished) and provided inputs; ensure it resolves to a boolean.

## Troubleshooting
- Condition never true: Verify the condition expression and referenced variable names (e.g., use index, start, step, end). Test with a simple 'True' to confirm flow.
- Loop finishes immediately: Check that step sign aligns with start and end (e.g., for descending loops use a negative step and end < start).
- Data not updating between iterations: Ensure you connected data/aux outputs from Loop Open to the corresponding inputs on Loop Close.
- Unexpected index value: If using index_override, confirm you pass a valid integer and update logic correctly.
- No execution after Loop Open: If condition is empty or evaluates to False, the loop will not run; provide a valid condition or adjust inputs.
