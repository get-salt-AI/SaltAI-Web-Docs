# While Loop Open (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Opens a legacy while-loop block and emits a flow-control token plus up to five initial values that will circulate through the loop. It does not evaluate the loop condition itself; it simply initializes the loop and passes values forward to be handled by the corresponding close node.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/saltwhileloopopen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a loop when building a legacy while-style control flow. Connect its FLOW_CONTROL output to SaltWhileLoopClose to create the loop, and wire any initial_value sockets to seed the variables that will iterate. The actual loop continuation condition is enforced at the close node. Prefer the newer Loop Open/Close nodes for new workflows.

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
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Initial condition value for the loop. Provided for compatibility; the continuation logic is handled by the close node.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">initial_value0</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First value to carry into the loop. Can be any type.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second value to carry into the loop. Can be any type.</td><td style="word-wrap: break-word;">start</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third value to carry into the loop. Can be any type.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">initial_value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fourth value to carry into the loop. Can be any type.</td><td style="word-wrap: break-word;">3.14</td></tr>
<tr><td style="word-wrap: break-word;">initial_value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fifth value to carry into the loop. Can be any type.</td><td style="word-wrap: break-word;">{'key': 'value'}</td></tr>
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
<tr><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">Flow-control token to establish the loop and link to the corresponding close node.</td><td style="word-wrap: break-word;"><flow-control-token></td></tr>
<tr><td style="word-wrap: break-word;">value0</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of initial_value0 for use inside the loop body.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of initial_value1 for use inside the loop body.</td><td style="word-wrap: break-word;">start</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of initial_value2 for use inside the loop body.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of initial_value3 for use inside the loop body.</td><td style="word-wrap: break-word;">3.14</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of initial_value4 for use inside the loop body.</td><td style="word-wrap: break-word;">{'key': 'value'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is deprecated. Prefer using Loop Open and Loop Close for new designs.
- The loop condition is not enforced here; it must be connected and evaluated at SaltWhileLoopClose.
- Supports up to five wildcard payload sockets (initial_value0–4) that circulate through the loop.
- All initial_value inputs are optional; missing values propagate as empty/None and should be handled in your graph.
- Must be paired with SaltWhileLoopClose to form a functioning loop.

## Troubleshooting
- No loop occurs: Ensure FLOW_CONTROL from this node is connected to SaltWhileLoopClose and that SaltWhileLoopClose receives a valid condition input.
- Values are None inside the loop: Provide initial_value inputs or ensure upstream nodes produce outputs before this node executes.
- Socket mismatch errors: Keep payload counts consistent between the open and close nodes; both sides expect up to five values in the same order.
- Unexpected infinite looping: Check the condition wired into SaltWhileLoopClose and verify it becomes false when the loop should terminate.
