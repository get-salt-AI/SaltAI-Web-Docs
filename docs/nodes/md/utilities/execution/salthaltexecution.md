# Halt Execution

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Ends the current workflow early on demand or based on a condition. It can either cancel the entire run immediately or block any downstream nodes connected after it, while passing through data when not halting. Designed to sit inline in your data path as a passthrough until triggered.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/execution/salthaltexecution.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Place this node between two steps in your workflow where you may want to stop execution conditionally. Use the 'disable' input to control whether halting is active, select the method to either cancel the whole run or block only subsequent nodes, and optionally surface an alert when the halt is triggered. Connect 'run_after' to any upstream node’s output if you need to enforce that this node executes after that node completes.

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
<tr><td style="word-wrap: break-word;">disable</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When True, prevents halting so execution continues; when False, allows this node to halt execution if triggered.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">method</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Selects how halting occurs. Off (Cancel Prompt): interrupts the entire run immediately. On (Block Execution): allows the run to continue elsewhere but prevents any nodes connected after this node from executing.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">alert_on_trigger</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When True, raises an alert at the moment halting occurs to make the interruption explicit and prevent cached downstream execution.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">The data to pass through this node. When not halting, this value is forwarded to the output unchanged.</td><td style="word-wrap: break-word;">Any data object (e.g., text, image, or structured data)</td></tr>
<tr><td style="word-wrap: break-word;">run_after</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Optional dependency input used only to enforce that this node runs after the connected node completes. Does not affect data.</td><td style="word-wrap: break-word;">Connect the output of an upstream step purely to control execution order</td></tr>
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
<tr><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Passes through the input when execution is not halted. If 'Cancel Prompt' is triggered, returns a null-like value. If 'Block Execution' is triggered, returns a special signal object to stop downstream nodes attached to this node.</td><td style="word-wrap: break-word;">Same data as provided to 'input' when not halted</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Halting is conditional**: Halting only occurs if 'disable' is False and there is an actual input value.
- **Two methods**: 'Cancel Prompt' interrupts the entire execution; 'Block Execution' prevents only nodes connected after this node from running.
- **Alert behavior**: Enabling 'alert_on_trigger' raises an alert at the time of interruption to stop further processing.
- **Placement matters**: Put this node inline with your data flow; it acts as a passthrough until halting.
- **Order control**: Use 'run_after' solely to ensure this node executes after a specific upstream node.
- **Platform support**: The 'Block Execution' method requires platform support; if unsupported, an error will be raised when attempting to block.

## Troubleshooting
- **Node does not halt**: Ensure 'disable' is set to False and that a valid value is provided to 'input'.
- **Downstream nodes still run after selecting Block Execution**: Verify your environment supports execution blocking; update your environment if necessary.
- **Entire run not stopping with Cancel Prompt**: Confirm 'method' is Off for 'Cancel Prompt' and 'alert_on_trigger' is enabled if you need an explicit interruption.
- **Node executes too early**: Connect an upstream node to 'run_after' to enforce execution order.
- **No output when not halting**: Check that 'input' has a valid value and 'disable' is True (or halting conditions aren't met) so the data passes through.
