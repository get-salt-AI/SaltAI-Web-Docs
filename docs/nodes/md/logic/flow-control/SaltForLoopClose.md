# For Loop Close (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Completes a counted for-loop started by For Loop Open and returns the final loop-carried values. Internally, it drives the loop to run a fixed number of iterations by decrementing an internal counter until zero, then exposes the final values from the loop.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/SaltForLoopClose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node as the closing counterpart to For Loop Open when you need a fixed-iteration loop with one or more loop-carried variables (accumulators). Connect the flow_control output from For Loop Open to this node’s flow_control input, optionally supply initial_value inputs for your loop-carried data, and consume the value outputs as the final results after all iterations.

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
<tr><td style="word-wrap: break-word;">flow_control</td><td>True</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">The loop context produced by For Loop Open. It contains the loop counter and state required to execute and finalize the for-loop.</td><td style="word-wrap: break-word;">Output from For Loop Open's flow_control</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1..initial_valueN</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Optional loop-carried initial values. Each initial_valueX seeds a corresponding valueX output. Provide as many as needed to track data across iterations (e.g., accumulators, lists, objects). Note: initial_value0 is reserved internally for the loop counter and is not exposed here.</td><td style="word-wrap: break-word;">initial_value1: 0 (INT accumulator), initial_value2: [] (LIST), initial_value3: {"sum": 0} (DICT)</td></tr>
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
<tr><td style="word-wrap: break-word;">value1..valueN</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Final loop-carried results after the for-loop completes. Each valueX corresponds to the progression of initial_valueX through all iterations.</td><td style="word-wrap: break-word;">value1: 45 (final INT sum), value2: [items...] (final LIST), value3: {"sum": 45} (final DICT)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Deprecated**: Use Loop Open and Loop Close for new graphs; they offer a more flexible, unified looping interface.
- **Pairing Required**: Must be paired with For Loop Open. Do not connect flow_control from unrelated sources.
- **Loop-Carried State**: Provide only initial_value1 and above; initial_value0 is reserved for the internal counter and handled automatically.
- **Type-Agnostic Values**: initial_value and value outputs are wildcard-typed and can carry any data type.
- **Fixed Iteration Behavior**: The loop runs a preset number of times determined by the opening node’s configuration; the close node finalizes and emits the last values.

## Troubleshooting
- **No outputs or None values**: Ensure flow_control is connected from a matching For Loop Open. Mismatched or missing flow_control prevents execution.
- **Unexpected iteration count**: Verify the start/step/end settings in For Loop Open; the close node uses the loop counter from the open node.
- **Missing value outputs**: If you need more value outputs, supply additional initial_value inputs (initial_value2, initial_value3, etc.) so the loop can carry those states.
- **Type errors downstream**: Since outputs are wildcard-typed, ensure downstream nodes accept the resulting types (e.g., INT, LIST, DICT).
- **Deprecated warning**: If you see deprecation notices, migrate to Loop Open/Loop Close by mapping your flow_control and initial_value/value pairs accordingly.
