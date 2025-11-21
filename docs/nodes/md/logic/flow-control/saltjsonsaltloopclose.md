# JSON Loop Close

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Completes and advances a JSON Loop created by JSON Loop Open. It evaluates a condition to decide whether to continue iterating or finish, and passes along final auxiliary values when the loop ends. This node emits a FINISHED? flag that is True only when the loop has completed or has been terminated by the condition.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltjsonsaltloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node in tandem with JSON Loop Open to iterate over a JSON array. Connect the LOOP output from JSON Loop Open to this node. Optionally provide auxiliary inputs to carry state across iterations. Set the condition to control whether the loop should continue; when the loop is done, FINISHED? will be True and the final auxiliary values are output.

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
<tr><td style="word-wrap: break-word;">LOOP</td><td>True</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">The loop state dictionary from JSON Loop Open. Required to track index, finish state, and control iteration.</td><td style="word-wrap: break-word;">{'id': 1, 'index': 0, 'finished': False, 'total_items': 3, 'last_id': 42}</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Expression evaluated each step to decide whether to continue. If it evaluates to False or errors, the loop finalizes and FINISHED? becomes True.</td><td style="word-wrap: break-word;">index < total_items - 1</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Primary auxiliary data carried through each iteration and returned when the loop finishes.</td><td style="word-wrap: break-word;">{'accumulator': []}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Secondary auxiliary data slot to persist state across iterations.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Tertiary auxiliary data slot to persist state across iterations.</td><td style="word-wrap: break-word;">partial_result</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Quaternary auxiliary data slot to persist state across iterations.</td><td style="word-wrap: break-word;">{'seen_ids': [1, 2]}</td></tr>
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
<tr><td style="word-wrap: break-word;">FINISHED?</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True when the loop has completed or has been terminated by the condition. Otherwise the loop continues.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Final value of the primary auxiliary data after the loop completes.</td><td style="word-wrap: break-word;">{'accumulator': ['a', 'b', 'c']}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Final value of the secondary auxiliary data after the loop completes.</td><td style="word-wrap: break-word;">27</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Final value of the tertiary auxiliary data after the loop completes.</td><td style="word-wrap: break-word;">done</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">any</td><td style="word-wrap: break-word;">Final value of the quaternary auxiliary data after the loop completes.</td><td style="word-wrap: break-word;">{'seen_ids': [1, 2, 3]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Condition behavior**: The condition is a string expression evaluated against the loop state and provided inputs. If it evaluates to False or throws an error, the loop finalizes and FINISHED? outputs True.
- **Required connection**: LOOP must be connected from the JSON Loop Open node; otherwise the node cannot function.
- **Aux persistence**: Only auxiliary inputs that are wired into this node are preserved and returned when the loop finishes.
- **Early termination**: You can terminate the loop early by setting a condition that becomes False before all items are processed.
- **Empty list handling**: If the JSON array is empty, the loop finalizes immediately and FINISHED? is True.

## Troubleshooting
- **FINISHED? never becomes True**: Verify that the condition can become False or that the input list has finite length. Ensure LOOP is properly connected from JSON Loop Open.
- **Loop stops immediately**: Check the condition string; if it evaluates to False at the first step or is invalid, the loop will finalize immediately.
- **Aux outputs are None**: Make sure aux, aux2, aux3, and aux4 are connected into this node during the loop. Unwired auxiliaries will not carry state.
- **Condition errors**: If the condition references variables that do not exist (e.g., misspelled 'index' or 'total_items'), it will be treated as False and finalize the loop. Use available loop variables from LOOP such as index and total_items.
