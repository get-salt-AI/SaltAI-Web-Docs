# For Loop Open (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Opens a counted loop that will iterate a specified number of times. It initializes and exposes a loop control handle plus pass-through values that will be available to the loop body. This node is deprecated and maintained for compatibility; prefer the newer Loop Open/Close nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltforloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a counted loop. Set 'remaining' to the number of iterations you want. Wire any auxiliary values you want to carry through the loop into the optional initial_value inputs. Connect its 'flow_control' (and any passthrough values) into the loop body and finish the loop with a matching For Loop Close node.

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
<tr><td style="word-wrap: break-word;">remaining</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">How many iterations the loop should perform. If 0, the loop body will not execute.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Optional value passed into the loop as value1; typically used to carry and update state across iterations.</td><td style="word-wrap: break-word;">some initial data</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Optional value passed into the loop as value2.</td><td style="word-wrap: break-word;">another value</td></tr>
<tr><td style="word-wrap: break-word;">initial_value3</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Optional value passed into the loop as value3.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">initial_value4</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Optional value passed into the loop as value4.</td><td style="word-wrap: break-word;">42</td></tr>
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
<tr><td style="word-wrap: break-word;">flow_control</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">Loop control handle that must be connected to the corresponding For Loop Close node to form a valid loop.</td><td style="word-wrap: break-word;">flow_handle_ref</td></tr>
<tr><td style="word-wrap: break-word;">remaining</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The starting iteration count provided to the loop.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Pass-through of initial_value1 for use inside the loop.</td><td style="word-wrap: break-word;">some initial data</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Pass-through of initial_value2 for use inside the loop.</td><td style="word-wrap: break-word;">another value</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Pass-through of initial_value3 for use inside the loop.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Pass-through of initial_value4 for use inside the loop.</td><td style="word-wrap: break-word;">42</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Deprecated**: This node is deprecated; prefer using Loop Open/Close for new workflows.
- **Pairing required**: Must be paired with a For Loop Close node. Do not mix with other loop close nodes.
- **Iteration count**: The loop will not execute if 'remaining' is 0. A hidden 'initial_value0' can override 'remaining' when provided.
- **State passthrough**: initial_value1–initial_value4 are passed to the loop body as value1–value4 for stateful updates across iterations.
- **Type flexibility**: Optional values accept any type but should remain consistent across iterations to avoid type conflicts in downstream nodes.

## Troubleshooting
- **Loop never runs**: Ensure 'remaining' is greater than 0 and that 'initial_value0' (if used) is not 0.
- **Loop does not close**: Verify that the For Loop Close node is used and its 'flow_control' input is connected to this node's 'flow_control' output.
- **Unexpected iteration count**: Check if a hidden 'initial_value0' is being supplied by an upstream connection, which overrides 'remaining'.
- **Values not updating across iterations**: Confirm you are wiring the updated values from For Loop Close back into the next iteration as intended.
- **Type errors**: Keep the types of initial_value1–initial_value4 consistent through the loop body to match downstream expectations.
