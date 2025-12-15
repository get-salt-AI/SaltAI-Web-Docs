# For Loop Close (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Closes a counter-based loop started by For Loop Open. It decrements the remaining counter each iteration and continues looping while remaining > 0, forwarding loop-carried values through to the next iteration.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltforloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use together with For Loop Open to iterate a fixed number of times. Connect For Loop Open's flow_control output to this node's flow_control input. Wire any loop-carried state (values you want to preserve and update across iterations) into initial_value1..4 on Close and back from value1..4 to the corresponding inputs on the Open side of your looped nodes.

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
<tr><td style="word-wrap: break-word;">flow_control</td><td>True</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">Loop control link from For Loop Open. Establishes the loop context.</td><td style="word-wrap: break-word;">Connected from For Loop Open: flow_control</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">First loop-carried value. Provide the updated value from inside the loop to be preserved into the next iteration.</td><td style="word-wrap: break-word;">Accumulated list or running total</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Second loop-carried value.</td><td style="word-wrap: break-word;">Intermediary computation result</td></tr>
<tr><td style="word-wrap: break-word;">initial_value3</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Third loop-carried value.</td><td style="word-wrap: break-word;">Status dictionary</td></tr>
<tr><td style="word-wrap: break-word;">initial_value4</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Fourth loop-carried value.</td><td style="word-wrap: break-word;">Any data type you need to persist</td></tr>
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
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final or next-iteration value corresponding to initial_value1.</td><td style="word-wrap: break-word;">Updated running total</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final or next-iteration value corresponding to initial_value2.</td><td style="word-wrap: break-word;">Transformed data</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final or next-iteration value corresponding to initial_value3.</td><td style="word-wrap: break-word;">State object</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final or next-iteration value corresponding to initial_value4.</td><td style="word-wrap: break-word;">Auxiliary payload</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is deprecated. Prefer using Loop Open and Loop Close for new workflows.
- Works only when paired with For Loop Open. It uses the internal loop 'remaining' counter and continues while remaining > 0.
- Loop-carried values are optional; connect only the ones you need to persist across iterations.
- If not all initial_value sockets are provided, their corresponding outputs will pass through None.
- This node internally decrements the remaining counter each iteration and stops when it reaches zero.

## Troubleshooting
- Loop never ends: Ensure the remaining value from For Loop Open is a positive integer. The loop stops when remaining reaches 0.
- Values not updating between iterations: Confirm you route the outputs (value1..4) from inside the loop back into the corresponding initial_value inputs on the next pass.
- Unexpected None outputs: Provide all required initial_value inputs, or ensure upstream nodes inside the loop produce values before Close executes.
- Loop does not start: Verify For Loop Open is correctly connected to this node's flow_control input and that the initial remaining count is >= 1.
