# Halt Execution

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Stops the current workflow either by canceling remaining execution globally or by blocking any downstream nodes connected after this point. It acts as a passthrough when not triggered, preserving the input data. You can optionally raise an alert when the halt occurs to ensure no further processing continues.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/execution/salthaltexecution.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Place this node inline in your workflow where you may want to conditionally stop or block subsequent execution. Wire the main data stream into 'input' and, if ordering matters, connect a dependency into 'run_after' to ensure this node runs after a specific step. Use 'disable' as a condition switch (it can be driven by prior logic) and select the halt method: Cancel Prompt (interrupt immediately) or Block Execution (prevent nodes after this one from running).

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
<tr><td style="word-wrap: break-word;">disable</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If True, prevents this node from halting execution. If False, the node will halt when it runs (provided 'input' is not None).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">method</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Selects how to halt execution. Off = Cancel Prompt (interrupts remaining execution). On = Block Execution (allows the workflow to continue elsewhere but prevents nodes downstream of this node from running).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">alert_on_trigger</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If True, raises an alert/error when the node halts execution, ensuring no additional cached or queued steps proceed.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Any data to pass through this node. When halting, behavior depends on 'method'; otherwise, this value is forwarded unchanged.</td><td style="word-wrap: break-word;">Any value (e.g., text, number, image handle, object)</td></tr>
<tr><td style="word-wrap: break-word;">run_after</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Optional dependency-only input to ensure this node executes after the connected node. Not used for data; sequencing only.</td><td style="word-wrap: break-word;">Output of a preceding node to enforce order</td></tr>
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
<tr><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Passes through the input when not halting. When Cancel Prompt triggers, returns a null-like value. When Block Execution triggers, returns a blocker object that prevents downstream nodes from executing.</td><td style="word-wrap: break-word;">Same data type as 'input' when continuing; null/blocker when halting</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Trigger condition**: Halting only occurs when 'disable' is False and 'input' is not None.
- **Two modes**: Cancel Prompt interrupts execution immediately; Block Execution allows other branches to continue but prevents nodes connected after this one from running.
- **Alerts**: Enabling 'alert_on_trigger' raises an alert/error to ensure the stop is enforced and visible.
- **Placement**: This node must be placed inline in the path you want to stop; it acts as a passthrough when not halting.
- **Ordering**: Use 'run_after' to enforce execution order if needed; it is not a data input.
- **Platform support**: Block Execution requires platform support for execution blocking; if unavailable, an error may be raised.
- **UI text**: The node provides a UI message indicating whether execution continued or was halted.

## Troubleshooting
- **Node does not halt**: Ensure 'disable' is False and that 'input' is wired and not None. Verify you're executing the path containing this node.
- **Block Execution not supported**: If you see an error about blocking execution, update your environment or use Cancel Prompt instead.
- **Execution continues unexpectedly**: Confirm the node is placed on the actual execution path you intend to stop and consider using 'run_after' to enforce order.
- **Alert causes an error**: This is expected when 'alert_on_trigger' is True; disable it if you want a silent halt behavior.
- **Downstream still runs from cache**: Use 'alert_on_trigger' to force an alert/error that prevents any further cached steps from proceeding.
