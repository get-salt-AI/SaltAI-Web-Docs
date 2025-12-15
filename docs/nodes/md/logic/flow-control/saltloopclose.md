# Loop Close

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Terminates or continues an iterative Loop. It evaluates a condition each pass: if the condition is false or the loop has reached its end, it outputs FINISHED? = True and returns the final Data/Aux values; otherwise it schedules the next iteration, passing through the current Data/Aux state.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use with a Loop Open node to repeat a section of your workflow. Connect the LOOP output from Loop Open into this node's LOOP input. Wire any Data/Aux values you want to carry across iterations from Loop Open to this node (and back into the next iteration via Loop Open). Set the condition to control when the loop should continue; leave it as True to rely solely on the Loop Open's range/step controls, or specify an expression that becomes false to stop the loop.

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
<tr><td style="word-wrap: break-word;">LOOP</td><td>True</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Loop state dictionary coming from Loop Open. It contains the current index and status used to manage iterations.</td><td style="word-wrap: break-word;">{ "id": 123, "index": 0, "finished": false, "last_id": 456 }</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Expression that must evaluate to True to continue iterating. When it evaluates to False, the loop finalizes and FINISHED? becomes True.</td><td style="word-wrap: break-word;">index < 10 and data['loss'] > 0.01</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Primary data carried through the loop. Provide the current iteration's value; it will be output when the loop finishes.</td><td style="word-wrap: break-word;">{"loss": 0.123, "epoch": 2}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary data carried through the loop.</td><td style="word-wrap: break-word;">["step1", "step2"]</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Second auxiliary data slot.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Third auxiliary data slot.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Fourth auxiliary data slot.</td><td style="word-wrap: break-word;">"running-total"</td></tr>
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
<tr><td style="word-wrap: break-word;">FINISHED?</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True when the loop has completed (either condition is false or the loop reached its end).</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final Data value after the loop completes.</td><td style="word-wrap: break-word;">{"loss": 0.008, "epoch": 10}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final Aux value after the loop completes.</td><td style="word-wrap: break-word;">["step1", "step2", "step3"]</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final Aux2 value after the loop completes.</td><td style="word-wrap: break-word;">84</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final Aux3 value after the loop completes.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final Aux4 value after the loop completes.</td><td style="word-wrap: break-word;">"complete"</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Condition evaluation**: The condition is evaluated each iteration with access to loop variables (such as index and finished) and the provided Data/Aux inputs.
- **Finish behavior**: FINISHED? becomes True when the loop is done; at that point, the outputs hold the final Data/Aux values.
- **Data passthrough**: To preserve state across iterations, wire Data/Aux outputs from Loop Open to the corresponding inputs of Loop Close, and ensure Loop Open is configured to feed these values back into the next iteration.
- **Empty condition**: An empty condition is treated as True, which continues the loop until the Loop Open’s range logic finishes it.
- **Engine requirements**: Requires a runtime that supports dynamic execution of looped subgraphs. Keep your Salt runtime up-to-date.

## Troubleshooting
- **Loop never ends**: Ensure the condition eventually evaluates to False or that Loop Open’s range (start/step/end) will complete. Verify that step is not zero unless you rely solely on the condition.
- **FINISHED? never True**: Confirm the LOOP output from Loop Open is connected to the LOOP input of this node and that the condition can become False. Check that Loop Open’s termination criteria are reachable.
- **Data/Aux not preserved**: Make sure Loop Open outputs for data/aux are wired into this node’s corresponding inputs (and that Loop Open receives them again for the next iteration).
- **Condition errors**: If the loop immediately stops or behaves unexpectedly, check the condition expression for syntax issues or undefined variables.
- **Runtime/version errors**: If you see errors about unsupported loop execution, update your Salt environment to a version that supports dynamic loop execution.
