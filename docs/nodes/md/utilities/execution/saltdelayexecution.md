# Delay Execution

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Pauses workflow execution for a specified number of seconds, then forwards the input unchanged. Accepts any data type and simply delays its passage downstream. Useful for timing control and coordinating steps that require a pause.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/execution/saltdelayexecution.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to introduce a timed pause in a workflow—such as rate limiting, synchronization with external processes, or staging multi-step operations. Set the delay in seconds (fractions allowed) and optionally pass any data through; it will be emitted after the delay.

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
<tr><td style="word-wrap: break-word;">delay</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Number of seconds to wait before forwarding the input. Supports fractional values for sub-second delays.</td><td style="word-wrap: break-word;">2.5</td></tr>
<tr><td style="word-wrap: break-word;">input</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Any data to pass through after the delay (e.g., image, text, number, or structured data). If not provided, the node outputs a null/empty value after waiting.</td><td style="word-wrap: break-word;">Any value (e.g., "Ready", 42, an image object, or a data payload)</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The same data received on the input, emitted after the specified delay. If no input was provided, outputs a null/empty value.</td><td style="word-wrap: break-word;">Same as the provided input (e.g., "Ready", 42, image object)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Blocking behavior**: This node blocks execution for the specified duration; downstream nodes will wait until the delay finishes.
- **Fractional seconds supported**: Use values like 0.5 for half a second or 0.001 for one millisecond.
- **No transformation**: The data is not modified—it's forwarded as-is after the delay.
- **Empty output if no input**: If the input is not connected, the node still waits and then outputs a null/empty value.
- **Timing precision**: Very small delays may be limited by system timer resolution and are not guaranteed to be exact.

## Troubleshooting
- **Workflow appears stuck**: Check the delay value; large numbers (e.g., 300) will pause execution for several minutes.
- **No data after delay**: Ensure the input is connected. When not connected, the node outputs a null/empty value after waiting.
- **Delay not precise**: Sub-millisecond or very small delays may not be accurate due to OS scheduling. Increase the delay or avoid extremely small values.
- **Downstream timing issues**: Remember this node is blocking; consider placing it only where a pause is acceptable for the entire branch.
