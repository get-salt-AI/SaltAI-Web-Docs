# Delay Execution

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Pauses workflow execution for a specified duration, then forwards the provided data unchanged. Supports fractional seconds for sub-second delays and works with any input type.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/execution/saltdelayexecution.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to introduce a wait between steps, e.g., to pace requests, coordinate time-sensitive operations, or add a buffer before downstream processing. Place it inline in your workflow: connect the data you want to pass through to the optional input, set the desired delay in seconds, and route the output to subsequent nodes.

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
<tr><td style="word-wrap: break-word;">delay</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Number of seconds to wait before passing the input forward. Supports fractional values for sub-second delays.</td><td style="word-wrap: break-word;">2.5</td></tr>
<tr><td style="word-wrap: break-word;">input</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Any data to be forwarded after the delay completes. If not provided, the node returns a null/None value.</td><td style="word-wrap: break-word;"><any data></td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The same data received on the input, forwarded after the delay. If no input was provided, this will be null/None.</td><td style="word-wrap: break-word;"><same as input></td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Blocking behavior**: The node synchronously waits; long delays pause downstream execution until completion.
- **Fractional seconds**: You can specify decimals (e.g., 0.001 for ~1 ms); real timing accuracy depends on the environment.
- **Pass-through**: Output is identical to the input; no transformation is applied.
- **No input case**: If no input is connected, the output will be null/None.
- **Timeout risk**: Very long delays may cause workflow timeouts depending on your runtime limits.
- **Non-interruptible sleep**: Once waiting starts, it cannot be canceled mid-sleep from within the node.

## Troubleshooting
- **Workflow appears stalled**: Verify the delay value isn't set too high; reduce the delay and try again.
- **Downstream node errors on type**: While this node accepts/outputs any type, ensure downstream nodes support the specific data type you are passing.
- **Output is null/None**: Connect the input or provide data to the optional input field.
- **Timing seems imprecise**: Minor variations can occur due to system scheduling; avoid relying on this node for high-precision timing.
- **Runtime limit exceeded**: Decrease the delay duration or adjust environment limits if possible.
