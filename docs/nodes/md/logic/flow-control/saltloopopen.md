# Loop Open

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Initializes a loop and outputs loop metadata plus up to five data channels to iterate over. It can operate as a for-loop (using start/step/end), a while-loop (using a boolean expression), or a combination of both, stopping when either the index range is exhausted or the condition evaluates to False.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to start a looped section of your workflow. Connect its LOOP output to the LOOP input of a Loop Close node. Provide start, step, end for for-loop style iteration, and/or a condition expression for while-loop style control. Pass any data you want to carry across iterations through the data and aux outputs into the corresponding inputs of the Loop Close node so they are preserved and updated each cycle.

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
<tr><td style="word-wrap: break-word;">start</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Initial loop index. Used with step and end for for-loop behavior.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">step</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Increment (positive or negative) applied to the index each iteration. Set to 0 to disable index progression (for pure while-loop).</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">end</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Terminal index used to determine completion for for-loop behavior.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Boolean expression evaluated each iteration. The loop proceeds only if it evaluates to True.</td><td style="word-wrap: break-word;">index < 10 and finished == False</td></tr>
<tr><td style="word-wrap: break-word;">index_override</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Manually set the current loop index for the next iteration (advanced control or manual reset).</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Primary data payload to iterate and carry through the loop.</td><td style="word-wrap: break-word;">{'items': [1, 2, 3]}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary data payload to iterate and carry through the loop.</td><td style="word-wrap: break-word;">working buffer</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Additional auxiliary data channel.</td><td style="word-wrap: break-word;">[0.1, 0.2]</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Additional auxiliary data channel.</td><td style="word-wrap: break-word;">{'state': 'init'}</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Additional auxiliary data channel.</td><td style="word-wrap: break-word;">meta info</td></tr>
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
<tr><td style="word-wrap: break-word;">LOOP</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Loop metadata dictionary for the Loop Close node. Includes fields like id, index, start, step, end, finished, and internal references.</td><td style="word-wrap: break-word;">{'id': 12345, 'start': 1, 'end': 10, 'step': 1, 'index': 1, 'finished': False}</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Passthrough of the primary data payload for use within the loop.</td><td style="word-wrap: break-word;">{'items': [1, 2, 3]}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Passthrough of an auxiliary data payload for use within the loop.</td><td style="word-wrap: break-word;">working buffer</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Passthrough of an additional auxiliary data channel.</td><td style="word-wrap: break-word;">[0.1, 0.2]</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Passthrough of an additional auxiliary data channel.</td><td style="word-wrap: break-word;">{'state': 'iter-1'}</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Passthrough of an additional auxiliary data channel.</td><td style="word-wrap: break-word;">meta info</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The node supports for-loop behavior (start/step/end), while-loop behavior (condition only, set step to 0), or a combined mode where either stopping condition ends the loop.
- If the condition evaluates to False at Loop Open, downstream execution is blocked for that iteration. Ensure your condition is valid and evaluates as expected.
- Use index_override to jump the index to a specific value for the next iteration. If not provided, the index starts from start and advances by step.
- The LOOP output is a metadata dictionary intended to be fed into Loop Close. Do not treat it as user data.
- To preserve and update data across iterations, connect the data/aux outputs of Loop Open to the corresponding inputs of Loop Close.

## Troubleshooting
- Condition never becomes True: Verify the condition string and any variables you reference (e.g., index, start, end, finished). Ensure it evaluates to a boolean.
- Loop stops immediately: Check for off-by-one or sign issues. For positive step, end should be greater than start; for negative step, end should be less than start.
- No iteration progress with step 0: This is expected in while-loop mode; ensure your condition changes over time to avoid infinite looping.
- Data not preserved across iterations: Ensure the data and aux outputs from Loop Open are connected to the corresponding inputs on Loop Close.
- Unexpected index jumps: If using index_override, confirm the value you pass is within expected bounds and that you only set it when intended.
