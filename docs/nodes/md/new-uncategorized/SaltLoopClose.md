# Loop Close

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Closes and advances a Loop created by a Loop Open node. It evaluates a user-defined condition and either schedules the next iteration (carrying forward data/aux values) or stops the loop and returns final values. It also outputs a boolean flag indicating when the loop has finished.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/SaltLoopClose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node in tandem with Loop Open. Connect Loop Open's LOOP output to this node's LOOP input, route any iterative state (data/aux/aux2/aux3/aux4) through both nodes, and set a condition that controls whether to continue iterating. Downstream nodes can wait for FINISHED? to become True to proceed with post-loop processing.

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
<tr><td style="word-wrap: break-word;">LOOP</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop state dictionary produced by Loop Open. Establishes the loop context and provides index, step, progress, and other status values.</td><td style="word-wrap: break-word;">{'id': 12345, 'index': 0, 'step': 1, 'finished': False}</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A boolean expression evaluated against the loop variables and any provided inputs. If it evaluates to False, the loop stops and outputs final values.</td><td style="word-wrap: break-word;">index < 10 and condition_open</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Primary data being iterated or accumulated across iterations. Connect the matching output from Loop Open to preserve and update state.</td><td style="word-wrap: break-word;">{'images': ['img1.png', 'img2.png']}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Auxiliary data to carry across iterations.</td><td style="word-wrap: break-word;">{'count': 5}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Additional auxiliary slot for iterative state.</td><td style="word-wrap: break-word;">['a', 'b']</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Additional auxiliary slot for iterative state.</td><td style="word-wrap: break-word;">0.75</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Additional auxiliary slot for iterative state.</td><td style="word-wrap: break-word;">{'threshold': 0.9}</td></tr>
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
<tr><td style="word-wrap: break-word;">FINISHED?</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True when the loop has stopped (either because the condition evaluated to False or the loop reports finished).</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the primary iterative data after the loop completes, or the updated value passed to the next iteration.</td><td style="word-wrap: break-word;">{'images': ['img1.png', 'img2.png', 'img3.png']}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the auxiliary data after loop completion, or the updated value passed to the next iteration.</td><td style="word-wrap: break-word;">{'count': 10}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the second auxiliary slot.</td><td style="word-wrap: break-word;">['a', 'b', 'c']</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the third auxiliary slot.</td><td style="word-wrap: break-word;">0.95</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final value of the fourth auxiliary slot.</td><td style="word-wrap: break-word;">{'threshold': 0.9}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Pairing required**: Must be used with Loop Open. Connect Loop Open's LOOP output to this node's LOOP input.
- **Condition evaluation**: The condition is a string expression evaluated against loop variables (e.g., index, step, finished) and your connected inputs. An empty condition defaults to True.
- **Finish criteria**: The loop stops when LOOP.finished is True or when the close-side condition evaluates to False.
- **State persistence**: To persist and update iterative state, route data and aux outputs from Loop Open into the matching inputs of Loop Close on each cycle.
- **Multiple aux slots**: Up to four auxiliary slots (aux, aux2, aux3, aux4) are available for additional iterative state.
- **Output behavior**: When the loop ends, FINISHED? becomes True and outputs return the final values; during ongoing iterations, this node schedules the next iteration instead of immediately returning final values.

## Troubleshooting
- **No iteration occurs**: Ensure Loop Open's LOOP output is connected to Loop Close's LOOP input and that the open-side condition allows entry.
- **Loop ends immediately**: Your close-side condition may be False on the first check or LOOP.finished may already be True. Inspect index, step, end, and your condition expression.
- **Infinite or unexpected looping**: Revisit both open-side and close-side conditions and how index changes. If using index overrides upstream, verify they increment or terminate properly.
- **State not updating**: Make sure you're feeding updated data/aux values back into Loop Open via Loop Close's expansion path and that the names match (data ↔ data, aux ↔ aux, etc.).
- **Condition errors**: If the condition references variables, ensure they exist in the loop context (e.g., index) or are passed via inputs. Invalid expressions will cause the condition to evaluate unsuccessfully.
