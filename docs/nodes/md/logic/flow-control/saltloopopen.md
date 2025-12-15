# Loop Open

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Initializes a generalized loop that can behave like a for-loop, a while-loop, or a hybrid of both. It emits a LOOP state dictionary plus passthrough data slots to be fed into a matching Loop Close node for iterative processing. The loop can be driven by start/step/end and/or a boolean expression evaluated each iteration.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to automate repeated execution of a section of your workflow. Connect the LOOP output to the LOOP input of a Loop Close node. Configure start, step, and end for for-loop behavior, set condition for while-loop behavior, or combine both. Feed any data you want to persist/update across iterations via data and aux inputs; wire the corresponding outputs from Loop Open to the matching inputs on Loop Close to carry state forward.

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
<tr><td style="word-wrap: break-word;">start</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Initial loop index. Acts as the starting value for iteration.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">step</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Amount to increment (or decrement if negative) the loop index each iteration.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">end</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Boundary value at which the loop should stop. With positive step, iteration stops when index reaches or passes end; with negative step, when it crosses end in the opposite direction.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Expression that must evaluate to True for the loop to execute this iteration. Leave as 'True' to run purely by start/step/end. Common variables you can reference: start, end, step, index, finished, data, aux, aux2, aux3, aux4.</td><td style="word-wrap: break-word;">index < end and step != 0</td></tr>
<tr><td style="word-wrap: break-word;">index_override</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Manually override the current index. Useful for custom stepping or to jump iterations.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Primary payload to iterate over or accumulate/transform across iterations.</td><td style="word-wrap: break-word;">my_initial_state_object</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary payload carried through the loop alongside data.</td><td style="word-wrap: break-word;">{'running_total': 0}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Additional auxiliary payload carried through the loop.</td><td style="word-wrap: break-word;">['collected_items']</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Additional auxiliary payload carried through the loop.</td><td style="word-wrap: break-word;">intermediate_result</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Additional auxiliary payload carried through the loop.</td><td style="word-wrap: break-word;">{'flags': ['seen', 'validated']}</td></tr>
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
<tr><td style="word-wrap: break-word;">LOOP</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Dictionary describing current loop state. Typical keys: id, start, end, step, index, finished, last_id, condition_open.</td><td style="word-wrap: break-word;">{'id': 123456, 'start': 1, 'end': 10, 'step': 1, 'index': 1, 'finished': False, 'last_id': '<unique-id>'}</td></tr>
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Pass-through of the data payload for use in the loop body and preservation across iterations.</td><td style="word-wrap: break-word;">my_initial_state_object</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Pass-through of auxiliary payload 1.</td><td style="word-wrap: break-word;">{'running_total': 0}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Pass-through of auxiliary payload 2.</td><td style="word-wrap: break-word;">['collected_items']</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Pass-through of auxiliary payload 3.</td><td style="word-wrap: break-word;">intermediate_result</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Pass-through of auxiliary payload 4.</td><td style="word-wrap: break-word;">{'flags': ['seen', 'validated']}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Condition semantics**: The condition is a string expression evaluated with access to loop variables like index, start, end, step and your data/aux inputs. If empty, it defaults to True.
- **For vs While**: Set step to 0 to emulate a pure while-loop controlled only by condition. Provide both condition and step to create a hybrid loop that stops when either limit is reached.
- **Bounds and direction**: Positive step stops when index >= end; negative step stops when index <= end.
- **Index override**: Use index_override to jump to a specific index for the next iteration (advanced control).
- **Pairing required**: Always connect this node to a Loop Close node via the LOOP output to actually iterate.
- **Data persistence**: To preserve evolving state across iterations, wire the corresponding data/aux outputs from Loop Open into the matching inputs on Loop Close.

## Troubleshooting
- **Loop never runs**: Ensure condition evaluates to True on the first iteration and that start/step/end aren't immediately marking the loop as finished.
- **Infinite or stalled loop**: Verify that step is non-zero when using for-loop semantics and that condition will eventually become False; with step=0, ensure condition becomes False based on your data/aux updates.
- **Unexpected early stop**: Check end relative to start and the sign of step; a mismatched direction can end immediately.
- **Condition errors**: If referencing variables, confirm they exist (e.g., index, data, aux) and the expression is valid.
- **State not carrying over**: Make sure you connect data/aux outputs from Loop Open to the matching inputs of Loop Close so updates persist between iterations.
