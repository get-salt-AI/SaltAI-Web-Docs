# While Loop Close (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Terminates or continues a while-style loop. When the condition is true, it re-runs the nodes between the matching While Loop Open and this node, carrying forward the state values. When the condition becomes false, it stops the loop and outputs the final accumulated values.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Logic/Flow Control/SaltWhileLoopClose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node together with While Loop Open to build a while loop. Connect the flow_control output from While Loop Open to this node's flow_control input. Feed a boolean condition that determines whether to continue looping (true) or stop (false). Use the initial_value sockets to pass and update state between iterations. Prefer the newer Loop Open/Loop Close nodes for new workflows, as this node is deprecated.

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
<tr><td style="word-wrap: break-word;">flow_control</td><td>True</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">Loop linkage from the corresponding While Loop Open. Identifies the loop scope to repeat.</td><td style="word-wrap: break-word;">Link from While Loop Open</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, the loop body executes again; if false, the loop ends and outputs are returned.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">initial_value0</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">State value carried through the loop body and returned when the loop completes.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Additional state value carried through the loop.</td><td style="word-wrap: break-word;">any data</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Additional state value carried through the loop.</td><td style="word-wrap: break-word;">{'counter': 1}</td></tr>
<tr><td style="word-wrap: break-word;">initial_value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Additional state value carried through the loop.</td><td style="word-wrap: break-word;">[1, 2, 3]</td></tr>
<tr><td style="word-wrap: break-word;">initial_value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Additional state value carried through the loop.</td><td style="word-wrap: break-word;">intermediate result</td></tr>
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
<tr><td style="word-wrap: break-word;">value0</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final or current state value 0 after loop completion or iteration.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final or current state value 1.</td><td style="word-wrap: break-word;">final data</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final or current state value 2.</td><td style="word-wrap: break-word;">{'sum': 55}</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final or current state value 3.</td><td style="word-wrap: break-word;">[5, 4, 3]</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final or current state value 4.</td><td style="word-wrap: break-word;">done</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is deprecated. For new workflows, use Loop Open and Loop Close.
- The loop continues while condition is true. When condition becomes false, the node outputs the provided/accumulated values and ends the loop.
- Ensure the flow_control input is connected from the matching While Loop Open of the same loop.
- There are 5 state sockets (initial_value0 through initial_value4). Use them to pass data through iterations.
- If an error occurs during processing, the node falls back to returning the provided initial_value inputs.

## Troubleshooting
- Loop never ends: Verify that your condition will eventually become false and that your state updates move toward termination.
- Loop does not iterate: Ensure condition is true and that flow_control is linked from the correct While Loop Open.
- Unexpected output values: Confirm you are wiring the intended state signals into initial_value0..4 and updating them inside the loop body.
- Type or shape mismatches: Keep the data types consistent across iterations for each initial_value socket.
- Multiple loops interfering: Make sure each While Loop Close is paired with its corresponding While Loop Open via the flow_control link.
