# Halt Execution

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Stops the current workflow either by canceling the whole run immediately or by blocking any nodes that follow this node from executing. It can act as a passthrough when disabled, forwarding the input unchanged. Optional alerting can raise an exception when a halt occurs to ensure nothing else proceeds.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/execution/salthaltexecution.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Place this node inline in your workflow wherever you may need to stop execution based on a condition. Use it as a conditional guard: convert its Disable control to an input and drive it from logic (e.g., an IF selector) to halt only under certain circumstances. Connect any data you want to pass through to the Input; optionally wire Run_After from another node solely to enforce that this node runs after that node completes.

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
<tr><td style="word-wrap: break-word;">disable</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When True, prevents halting and allows execution to continue; when False, halting behavior can be triggered.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">method</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Halting method. Off = Cancel Prompt (interrupts the entire run immediately). On = Block Execution (prevents any nodes downstream of this node from running).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">alert_on_trigger</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If True and a halt occurs, raises an alert/exception to ensure no further processing continues.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Any data to pass through this node. Also used to determine whether to trigger halting.</td><td style="word-wrap: break-word;">some_data_object</td></tr>
<tr><td style="word-wrap: break-word;">run_after</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Optional dependency-only input to force this node to execute after the connected node. Not used for data processing.</td><td style="word-wrap: break-word;">dependency_token</td></tr>
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
<tr><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Passes through the same data when not halting. If Cancel Prompt is used and halting occurs, outputs null. If Block Execution is used and supported, outputs a special blocking token that prevents downstream nodes from running.</td><td style="word-wrap: break-word;">some_data_object</td></tr>
</tbody>
</table>
</div>

## Important Notes
- If Disable is True, this node will not halt execution and simply forwards the input.
- A halt is only triggered when Disable is False and the Input is not null.
- Block Execution mode requires runtime support for execution blocking; if unsupported, an error will be raised advising an update.
- Alert On Trigger will raise an exception when a halt occurs, fully stopping further processing and cached execution.
- Run_After is for execution ordering only and does not affect data.

## Troubleshooting
- Halting did not occur as expected: Ensure Disable is False and the Input is not null.
- Block Execution raised an error: Your runtime may not support execution blocking; update your environment to a version that supports it.
- Workflow fully stopped when halting: Disable Alert On Trigger if you prefer not to raise an exception and only block/cancel locally.
- Downstream nodes still executed after halting: Verify you used the desired method (Block Execution vs Cancel Prompt) and that execution blocking is supported.
- This node ran too early or out of order: Connect a node’s output to Run_After to enforce ordering.
