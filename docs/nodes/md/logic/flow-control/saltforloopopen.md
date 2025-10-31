# For Loop Open (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Opens a legacy for-style loop. It initializes a loop counter and passes through up to four auxiliary values to be carried across iterations. Internally it sets up a while-loop gate using the counter as the condition and must be paired with For Loop Close.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltforloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a deprecated for-loop sequence together with For Loop Close. Set 'remaining' to the number of iterations you want. Connect the 'flow_control' output to For Loop Close, and wire any state you want to preserve between iterations via value outputs to the corresponding 'initial_value' inputs on the close node. Prefer the newer Loop Open/Loop Close nodes for new workflows.

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
<tr><td style="word-wrap: break-word;">remaining</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of iterations to perform. Used as the loop condition; iterations proceed while this value remains non-zero. The close node decrements this each cycle.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First carried value passed into the loop body and back to the close node each iteration.</td><td style="word-wrap: break-word;">image tensor or integer accumulator</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second carried value passed through the loop.</td><td style="word-wrap: break-word;">list, dict, or any data</td></tr>
<tr><td style="word-wrap: break-word;">initial_value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third carried value passed through the loop.</td><td style="word-wrap: break-word;">string or model handle</td></tr>
<tr><td style="word-wrap: break-word;">initial_value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fourth carried value passed through the loop.</td><td style="word-wrap: break-word;">boolean flag</td></tr>
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
<tr><td style="word-wrap: break-word;">flow_control</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">Handle that identifies and links this loop instance to the matching For Loop Close.</td><td style="word-wrap: break-word;">FLOW_CONTROL token</td></tr>
<tr><td style="word-wrap: break-word;">remaining</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The current remaining-iterations counter exposed to downstream nodes.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of initial_value1 for use inside the loop body.</td><td style="word-wrap: break-word;">integer accumulator</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of initial_value2 for use inside the loop body.</td><td style="word-wrap: break-word;">image tensor</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of initial_value3 for use inside the loop body.</td><td style="word-wrap: break-word;">dict</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of initial_value4 for use inside the loop body.</td><td style="word-wrap: break-word;">boolean</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Deprecated**: Use Loop Open and Loop Close for new workflows.
- **Pairing required**: Must be connected to For Loop Close via the flow_control output; otherwise the loop will not iterate.
- **Counter handling**: The hidden initial_value0 overrides 'remaining' on entry and is typically fed from the close node to update the counter.
- **Iteration logic**: The actual decrement and continuation check are performed in the For Loop Close node; this open node just initializes and forwards values.
- **Type flexibility**: value1–value4 accept any data type and are preserved across iterations when properly connected.
- **Range limits**: 'remaining' accepts 0–100000. A value of 0 means the loop will not execute.
- **Error behavior**: On internal errors, the node returns default outputs and no expansion of the loop graph.

## Troubleshooting
- **Loop doesn't start**: Ensure 'remaining' > 0. A zero value prevents any iteration.
- **No iteration occurs**: Verify the flow_control output from For Loop Open is connected to For Loop Close and that the close node is configured to decrement and feed back the counter.
- **State not preserved**: Connect value outputs from this node to the corresponding 'initial_value' inputs on For Loop Close; mismatched or missing connections break state carry-over.
- **Close node errors or missing nodes**: For Loop Close relies on integer math and condition nodes; ensure the required nodes are available in your environment.
- **Unexpected remaining value**: The hidden initial_value0 overrides the visible 'remaining'. Check the connection from the close node feeding this hidden input.
