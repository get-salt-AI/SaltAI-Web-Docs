# Loop Close

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Finalizes or advances a running Loop started by Loop Open. It evaluates a user-supplied condition, decides whether the loop should continue, and, if continuing, schedules the next iteration while passing along updated data. When the loop stops, it outputs FINISHED? = True along with the final data values.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node together with Loop Open to create iterative sections in your workflow. Connect the LOOP output from Loop Open into the LOOP input here. Optionally pass data and aux inputs to carry and update state across iterations. Provide a condition expression to control whether the loop should continue; when it evaluates to False or the loop reaches its natural end, this node outputs FINISHED? = True with the final data.

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
<tr><td style="word-wrap: break-word;">LOOP</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Loop state dictionary from Loop Open. Required to identify the loop, current index, step, and finished status.</td><td style="word-wrap: break-word;">{'id': 123, 'start': 1, 'end': 10, 'step': 1, 'index': 3, 'finished': False, 'last_id': 'node_abc'}</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Expression that must evaluate to True to continue iterating. If blank, it is treated as True. You can reference loop variables (e.g., index, step, start, end, finished) and any data/aux inputs.</td><td style="word-wrap: break-word;">index < end and not finished</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Primary data to persist and update across iterations. Feed the corresponding output from Loop Open here to carry state through the loop.</td><td style="word-wrap: break-word;">{'items': [1, 2, 3], 'acc': 6}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Auxiliary data to persist/update across iterations.</td><td style="word-wrap: break-word;">{'debug': True}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Additional auxiliary data channel.</td><td style="word-wrap: break-word;">any value</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Additional auxiliary data channel.</td><td style="word-wrap: break-word;">[10, 20, 30]</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Additional auxiliary data channel.</td><td style="word-wrap: break-word;">{'meta': 'info'}</td></tr>
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
<tr><td style="word-wrap: break-word;">FINISHED?</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True when the loop has concluded (either the loop reached its end criteria or the condition evaluated to False).</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Final data value after the loop terminates. Mirrors the data input across iterations.</td><td style="word-wrap: break-word;">{'items': [1, 2, 3], 'acc': 6}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Final auxiliary data value after the loop terminates.</td><td style="word-wrap: break-word;">{'debug': False}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Final auxiliary data value after the loop terminates.</td><td style="word-wrap: break-word;">any value</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Final auxiliary data value after the loop terminates.</td><td style="word-wrap: break-word;">[10, 20, 30]</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Final auxiliary data value after the loop terminates.</td><td style="word-wrap: break-word;">{'meta': 'final'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Condition handling**: An empty condition string is treated as True. The condition is evaluated against loop variables and provided inputs each iteration.
- **Loop termination**: The loop ends when either LOOP.finished is True (from Loop Open) or the condition evaluates to False at Loop Close.
- **State persistence**: To persist or update values across iterations, connect the corresponding outputs from Loop Open into this node's inputs (data, aux, aux2, aux3, aux4).
- **Iteration advance**: When continuing, the node schedules the next iteration and advances index by step via the loop engine.
- **Execution engine requirement**: Uses dynamic graph expansion provided by the Salt execution engine. Ensure your environment supports loop-based graph expansion.
- **Aux channels**: Up to four auxiliary channels (aux through aux4) are supported for carrying multiple pieces of state.

## Troubleshooting
- **FINISHED? never becomes True**: Verify the condition eventually becomes False or that Loop Open is configured with an end/step that will reach completion. Ensure index and step are correct.
- **Loop stops immediately**: Check that your condition evaluates to True on the first pass. Referenced variables must exist and be correctly named (e.g., index, start, end, finished).
- **Data not preserved across iterations**: Make sure the outputs from Loop Open are wired to the matching inputs on Loop Close (data → data, aux → aux, etc.).
- **Node does not iterate**: Confirm LOOP is connected from Loop Open, and that Loop Open’s condition allows the first iteration to start.
- **Unexpected condition errors**: Ensure the condition is a valid expression and only references available variables. If in doubt, simplify the expression or add guards (e.g., check for None).
- **Performance with many iterations**: Large iteration counts can expand significant subgraphs. Consider reducing iterations or simplifying the loop body.
