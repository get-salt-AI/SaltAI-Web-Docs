# Loop Open

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Initializes a loop that can run a fixed number of iterations (for-loop style), conditionally (while-loop style), or a combination of both. It outputs a LOOP context object plus passthrough data channels that will be fed into the loop body and preserved across iterations when wired to a matching Loop Close node.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to start a looped section of your workflow. Connect the LOOP output to the LOOP input of a Loop Close node. Configure start, step, and end for a fixed iteration range, and/or provide a condition expression to control continuation. Pass any working data through the data and aux outputs to the Loop Close so changes are carried forward each iteration.

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
<tr><td style="word-wrap: break-word;">step</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Amount to increment (positive) or decrement (negative) the index each iteration. Set to 0 to ignore range stepping and use only the condition.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">end</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Target boundary used with start and step to determine when the range-based loop is finished.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A boolean expression evaluated with loop context (e.g., index, start, step, end) and any passed data. The loop runs only while this evaluates to true. Use an empty string or "True" to always allow.</td><td style="word-wrap: break-word;">index < end and step != 0</td></tr>
<tr><td style="word-wrap: break-word;">index_override</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Manually override the current loop index. Useful for complex stepping or jumping to a specific iteration.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Primary data object to iterate on. Wire this to matching inputs on the Loop Close node to persist changes between iterations.</td><td style="word-wrap: break-word;">Image or JSON object</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Auxiliary data to carry through the loop and preserve between iterations.</td><td style="word-wrap: break-word;">Mask or list</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Second auxiliary data channel.</td><td style="word-wrap: break-word;">Integer counter</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Third auxiliary data channel.</td><td style="word-wrap: break-word;">Text prompt</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Fourth auxiliary data channel.</td><td style="word-wrap: break-word;">Settings dict</td></tr>
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
<tr><td style="word-wrap: break-word;">LOOP</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop context object (id, index, start, step, end, finished flag, etc.). Connect this to the LOOP input of Loop Close.</td><td style="word-wrap: break-word;">{"id": 123, "index": 1, "start": 1, "step": 1, "end": 10, "finished": false}</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of the primary data for iteration. Wire to Loop Close to persist updates.</td><td style="word-wrap: break-word;">Image or JSON object</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of auxiliary data channel.</td><td style="word-wrap: break-word;">Mask or list</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of second auxiliary data channel.</td><td style="word-wrap: break-word;">Integer counter</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of third auxiliary data channel.</td><td style="word-wrap: break-word;">Text prompt</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of fourth auxiliary data channel.</td><td style="word-wrap: break-word;">Settings dict</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Connect LOOP to Loop Close**: The LOOP output must be connected to a Loop Close node to establish and run the loop.
- **For-loop vs While-loop**: Use start/step/end for fixed iterations. Use condition for while-style continuation. Combine both: the loop stops when either the range completes or the condition becomes false.
- **Pure while-loop**: Set step to 0 to ignore range stepping and rely only on the condition.
- **Index override**: Supplying index_override forces the next iteration index. Use with care to avoid skipping beyond your intended bounds.
- **Data persistence**: To carry updated values across iterations, wire data/aux outputs from Loop Open to the corresponding inputs on Loop Close.
- **Blocking behavior**: If the condition evaluates to false at the start, the loop is prevented from executing and downstream nodes after Loop Open will not run.

## Troubleshooting
- **Loop never starts**: Ensure the condition evaluates to true initially (e.g., index < end when using range stepping). If you only want range control, set condition to True.
- **Loop stops immediately**: Check the sign of step relative to start and end. For increasing sequences, step should be positive with start < end; for decreasing sequences, step should be negative with start > end.
- **Data not updating across iterations**: Confirm the data and aux outputs from Loop Open are connected to the corresponding inputs on Loop Close.
- **Unexpected index jumps**: Remove or adjust index_override if it forces the index beyond the intended range.
- **Condition errors or unexpected results**: Simplify the condition expression and verify referenced variables (e.g., index, start, step, end) are valid and produce a boolean outcome.
- **Infinite loop risk**: When using step = 0, ensure the condition will eventually become false based on your loop body logic.
