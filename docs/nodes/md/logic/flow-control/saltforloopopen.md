# For Loop Open (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Opens a counter-based loop and exposes a flow-control handle plus counters and initial state values for iteration. This node does not iterate by itself; it prepares the loop and must be paired with a corresponding For Loop Close node to perform repeated execution.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltforloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to start a fixed-count loop. Connect its flow_control output to a For Loop Close node. Set the remaining input to the number of iterations you want. Optionally pass initial values (value1..value4) that will be threaded through each iteration by connecting them consistently between the Open and Close nodes. This node is deprecated; prefer the newer Loop Open/Loop Close nodes for enhanced functionality.

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
<tr><td style="word-wrap: break-word;">remaining</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of iterations to perform. Acts as the loop counter seed.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Optional initial data value to carry through the loop. Available to downstream nodes and preserved via the Close node.</td><td style="word-wrap: break-word;">any value</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Optional secondary initial data value to carry through the loop.</td><td style="word-wrap: break-word;">any value</td></tr>
<tr><td style="word-wrap: break-word;">initial_value3</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Optional third initial data value to carry through the loop.</td><td style="word-wrap: break-word;">any value</td></tr>
<tr><td style="word-wrap: break-word;">initial_value4</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Optional fourth initial data value to carry through the loop.</td><td style="word-wrap: break-word;">any value</td></tr>
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
<tr><td style="word-wrap: break-word;">flow_control</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">Flow-control handle that must be connected to a For Loop Close node to drive iteration.</td><td style="word-wrap: break-word;">Flow control token</td></tr>
<tr><td style="word-wrap: break-word;">remaining</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The initialized remaining-iterations counter value.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Pass-through of initial_value1 to seed the loop state.</td><td style="word-wrap: break-word;">any value</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Pass-through of initial_value2 to seed the loop state.</td><td style="word-wrap: break-word;">any value</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Pass-through of initial_value3 to seed the loop state.</td><td style="word-wrap: break-word;">any value</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Pass-through of initial_value4 to seed the loop state.</td><td style="word-wrap: break-word;">any value</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is deprecated. Prefer using Loop Open and Loop Close for new designs.
- Must be paired with For Loop Close; on its own this node does not execute iterations.
- The hidden initial_value0 input, if supplied, overrides the remaining counter initialization.
- Up to four auxiliary data values (value1..value4) can be threaded through the loop across iterations.
- remaining must be a non-negative integer; extremely large values may impact performance.

## Troubleshooting
- If the loop does not run, ensure the flow_control output is connected to a For Loop Close node.
- If iterations do not decrement as expected, confirm that For Loop Close is properly connected and that remaining was initialized correctly.
- If auxiliary values are not preserved, ensure the same value1..value4 outputs from Open feed the corresponding initial_value inputs on Close.
- If the loop stops immediately, check whether hidden initial_value0 is set to 0 or a falsy value by upstream logic.
