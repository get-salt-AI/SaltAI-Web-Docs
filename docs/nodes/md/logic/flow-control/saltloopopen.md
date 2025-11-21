# Loop Open

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Initializes and controls a loop segment in your workflow. Supports for-style iteration (start/step/end), while-style iteration (condition-based), or a hybrid of both. Emits a LOOP status object plus pass-through data channels that are maintained across iterations when wired through a corresponding Loop Close node.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to start a looped section of your workflow. Connect its LOOP output to the LOOP input of a Loop Close node. Configure start, step, and end for for-style iteration, and/or provide a condition expression for while-style control. Feed any state you want to persist across iterations via the data and aux inputs, and pass them into the Loop Close node to carry updates forward.

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
<tr><td style="word-wrap: break-word;">start</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Initial counter value for the loop index.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">step</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Amount to increment or decrement the index each iteration. Use 0 for a pure while-style loop.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">end</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Terminal value that determines when a for-style loop should stop. With positive step, the loop is considered finished when index >= end; with negative step, when index <= end.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A boolean expression evaluated each iteration to allow or prevent loop execution. If empty, treated as True. The expression can reference loop status fields and provided variables.</td><td style="word-wrap: break-word;">index < 20 and condition_open</td></tr>
<tr><td style="word-wrap: break-word;">index_override</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Manually override the current loop index. Useful for custom stepping logic, skipping ahead, or resetting.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Primary data payload to iterate on. Connect this output to the Loop Close input to persist changes across iterations.</td><td style="word-wrap: break-word;">{'count': 0, 'items': []}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary data payload to iterate on. Connect its output to the Loop Close input to persist changes.</td><td style="word-wrap: break-word;">{'stats': {'min': 0, 'max': 0}}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Additional auxiliary data channel.</td><td style="word-wrap: break-word;">[1, 2, 3]</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Additional auxiliary data channel.</td><td style="word-wrap: break-word;">intermediate_state</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Additional auxiliary data channel.</td><td style="word-wrap: break-word;">{'threshold': 0.75}</td></tr>
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
<tr><td style="word-wrap: break-word;">LOOP</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Loop status dictionary. Must connect to the LOOP input of a Loop Close node. Contains fields like id, start, end, step, index, finished, and last_id.</td><td style="word-wrap: break-word;">{'id': '<loop-id>', 'start': 1, 'end': 10, 'step': 1, 'index': 1, 'finished': False, 'last_id': '<node-id>'}</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Pass-through data for iteration. Connect to Loop Close to persist updates.</td><td style="word-wrap: break-word;">{'count': 1}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Pass-through auxiliary data for iteration.</td><td style="word-wrap: break-word;">{'sum': 10}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Additional pass-through auxiliary data channel.</td><td style="word-wrap: break-word;">[2, 4, 6]</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Additional pass-through auxiliary data channel.</td><td style="word-wrap: break-word;">phase-1</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Additional pass-through auxiliary data channel.</td><td style="word-wrap: break-word;">{'flag': True}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Hybrid control**: Configure both step/end and a condition to stop when either the index reaches the end or the condition becomes False.
- **While-style loop**: Set step to 0 and rely solely on the condition expression.
- **Index override**: Use index_override to jump to a specific index on the next iteration (e.g., skip or restart).
- **Data persistence**: To preserve changes to data/aux across iterations, wire Loop Open outputs into matching inputs on the Loop Close node.
- **Condition evaluation**: The condition is a string expression evaluated against variables including index, start, step, end, finished, and any provided inputs. An empty string is treated as True.
- **Flow blocking**: If the condition evaluates to False at the open, downstream execution is prevented for this pass.
- **Counting direction**: For positive step, the loop is considered finished when index >= end; for negative step, when index <= end.

## Troubleshooting
- **Loop never starts**: Ensure the condition expression evaluates to True on the first pass and that step/end allow at least one iteration.
- **Loop stops too early**: Check the sign of step relative to start and end. For descending loops, use a negative step and an end that is below start.
- **Condition errors**: If your condition references variables, confirm they exist in the loop context (e.g., index, start, step, end, finished, data/aux fields). Use a valid boolean expression string.
- **State not preserved**: Verify that data and aux outputs from Loop Open are connected to corresponding inputs on Loop Close; otherwise, updates won’t carry to the next iteration.
- **Unexpected index jumps**: Remove or correct index_override if unintentionally set by upstream nodes.
- **No downstream execution**: If nodes after Loop Open do not run, the condition likely evaluated to False; adjust the condition or initial parameters.
