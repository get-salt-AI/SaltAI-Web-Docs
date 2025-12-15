# Halt Execution

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Stops the current workflow on demand or based on a condition. It acts as a passthrough until triggered, then either cancels the entire run or blocks any downstream nodes connected after it. Optionally emits an alert when the halt occurs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/execution/salthaltexecution.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Insert this node mid-flow where you want the option to stop further processing. Feed your ongoing data into the Input so it can pass through when not halting. Use the 'method' to choose between fully canceling the run or only blocking subsequent nodes connected after this node. Combine with conditional logic (e.g., toggling 'disable' via another node) to halt only under specific conditions. Use 'run_after' to enforce execution order relative to another node.

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
<tr><td style="word-wrap: break-word;">disable</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When True, halting is disabled and execution continues. When False, the node can halt the run according to the selected method.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">method</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Selects the halt behavior: False = Cancel Prompt (interrupts the entire run immediately), True = Block Execution (prevents nodes that follow this one from running, while allowing other branches to continue).</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">alert_on_trigger</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If True, emits an alert/error when the node halts execution to make the stop explicit.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">The data stream to pass through this node. When halting is not triggered, this value is forwarded unchanged.</td><td style="word-wrap: break-word;">Any value or structure (e.g., an image object, text, or dict)</td></tr>
<tr><td style="word-wrap: break-word;">run_after</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Optional dependency-only input. Connect the output of a node here to ensure this node executes after that node completes (ordering aid; the value is not used).</td><td style="word-wrap: break-word;">Output from a prior node to enforce order</td></tr>
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
<tr><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Passes through the input data when not halting. When halting, returns a signal or empty value depending on the chosen method, preventing downstream processing.</td><td style="word-wrap: break-word;">Same value as 'input' if not halted; otherwise a stop/block signal or null-equivalent</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Passthrough behavior**: When halting is disabled or not triggered, the node returns exactly what it receives in 'input'.
- **Two halt modes**: Cancel Prompt (False) stops the entire run immediately. Block Execution (True) prevents only downstream nodes connected after this node from running.
- **Alert option**: Enabling 'alert_on_trigger' will surface a clear alert/error when a halt occurs, helping diagnose why/where the stop happened.
- **Ordering aid**: The 'run_after' input is only for execution order control and does not affect the data flowing through.
- **Conditional control**: Convert or drive 'disable' from other logic to create conditional halts (e.g., stop on validation failure).

## Troubleshooting
- **The node doesn't halt when expected**: Ensure 'disable' is set to False and 'input' is not empty/null. Verify that your condition logic actually sends False into 'disable' when the halt should occur.
- **Downstream nodes still run after Block Execution**: Confirm those nodes are directly or indirectly dependent on this node’s output. If they are not connected after this node, they may continue in parallel.
- **Entire run stops unexpectedly**: Check if 'method' is set to Cancel Prompt (False) and whether 'alert_on_trigger' is enabled, which will surface an alert/error.
- **Node executes too early or late**: Use the 'run_after' input to enforce ordering by connecting it to the node that must complete before this one runs.
- **No output when not halting**: Verify the upstream 'input' value is present and valid; the node forwards the 'input' unchanged when halting is not triggered.
