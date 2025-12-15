# While Loop Close (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Terminates or continues a while-style loop region that starts at While Loop Open. If the condition is false, it outputs the current/initial values and ends the loop. If the condition is true, it dynamically re-invokes the nodes contained between the matching Open and this Close to perform another iteration, passing along the current values.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltwhileloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node together with While Loop Open to create a while-style loop around a section of your workflow. Connect the FLOW_CONTROL output from While Loop Open to the flow_control input on this node. Provide a boolean condition that determines whether the loop should continue. Feed any loop-carried values through the initial_value inputs and connect the returned outputs to the corresponding inputs inside your looped region for state to persist across iterations. Because this node is deprecated, prefer the newer Loop Open/Loop Close nodes for new workflows.

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
<tr><td style="word-wrap: break-word;">flow_control</td><td>True</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">The loop linkage from the corresponding While Loop Open node. This establishes the loop boundary and internal region to repeat.</td><td style="word-wrap: break-word;"><FLOW_CONTROL_FROM_WHILE_LOOP_OPEN></td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls loop continuation. True triggers another iteration; False finalizes the loop and returns the accumulated values.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">initial_value0</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop-carried value slot 0. Provide the current value to carry into the next iteration. Returned as value0.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop-carried value slot 1. Provide the current value to carry into the next iteration. Returned as value1.</td><td style="word-wrap: break-word;">current_state</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop-carried value slot 2. Provide the current value to carry into the next iteration. Returned as value2.</td><td style="word-wrap: break-word;">0.75</td></tr>
<tr><td style="word-wrap: break-word;">initial_value3</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop-carried value slot 3. Provide the current value to carry into the next iteration. Returned as value3.</td><td style="word-wrap: break-word;">{'progress': 5}</td></tr>
<tr><td style="word-wrap: break-word;">initial_value4</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Loop-carried value slot 4. Provide the current value to carry into the next iteration. Returned as value4.</td><td style="word-wrap: break-word;">[1, 2, 3]</td></tr>
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
<tr><td style="word-wrap: break-word;">value0</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final or iterated value corresponding to initial_value0.</td><td style="word-wrap: break-word;">2</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final or iterated value corresponding to initial_value1.</td><td style="word-wrap: break-word;">updated_state</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final or iterated value corresponding to initial_value2.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final or iterated value corresponding to initial_value3.</td><td style="word-wrap: break-word;">{'progress': 10}</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final or iterated value corresponding to initial_value4.</td><td style="word-wrap: break-word;">[1, 2, 3, 4]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Deprecated**: Prefer using Loop Open and Loop Close for new workflows.
- **Pairing required**: Must be paired with a matching While Loop Open via the flow_control connection; otherwise it cannot determine the loop body.
- **Condition-driven**: The loop continues only when condition is True; when False, the node outputs the provided initial_value inputs as final results.
- **Dynamic expansion**: Internally clones and re-executes the nodes between the Open and Close on each iteration. The loop-carried values are forwarded via the initial_value inputs.
- **Hidden inputs are system-managed**: dynprompt and unique_id are automatically provided; do not connect these manually.
- **Fixed value slots**: Provides five generic value slots (0-4). Map your loop state consistently across iterations.
- **Error handling**: On internal errors, it returns the current initial_value inputs (i.e., it finalizes with pass-through values).

## Troubleshooting
- **Loop never runs**: Ensure the condition evaluates to True at least once. If it is always False, the node immediately returns the initial_value inputs.
- **No updates across iterations**: Make sure the outputs from nodes inside the loop are routed back into the corresponding initial_value inputs so state is carried forward.
- **Mismatched Open/Close**: Verify flow_control comes from the intended While Loop Open and that the loop body nodes reside between the linked Open and Close.
- **Unexpected early termination**: Check the condition wiring and values produced inside the loop. If the condition becomes False, the loop ends and returns the current values.
- **Graph expansion errors**: Complex or invalid connections inside the looped region can prevent expansion. Simplify the loop body and avoid references to nodes outside the Open/Close region unless intended.
