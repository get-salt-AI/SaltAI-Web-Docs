# Delay Execution

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Pauses the workflow for a specified number of seconds, then forwards the provided input unchanged. Supports fractional seconds for sub-second delays and accepts any data type as passthrough input.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/execution/saltdelayexecution.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to intentionally delay progression in a workflow—such as pacing requests to external services, coordinating timing with other processes, or inserting short pauses between steps. Place it inline where the output should be delayed before reaching downstream nodes.

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
<tr><td style="word-wrap: break-word;">delay</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Number of seconds to wait before forwarding the input. Supports fractional values for sub-second precision.</td><td style="word-wrap: break-word;">2.5</td></tr>
<tr><td style="word-wrap: break-word;">input</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Any data to pass through after the delay completes. If not provided, the node outputs null.</td><td style="word-wrap: break-word;">Any data type (e.g., an image object, text string, JSON payload)</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The same data received on the input, forwarded after the delay period. If no input was provided, outputs null.</td><td style="word-wrap: break-word;">Same as the provided input, unchanged</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node blocks execution for the specified time; downstream nodes will not run until the delay completes.
- Fractional seconds are supported (e.g., 0.001 for 1 millisecond).
- If no input is connected, the output will be null.
- Negative delays are invalid and will cause an error.
- Large delays will hold resources for the duration of the wait.

## Troubleshooting
- Delay has no effect: Ensure the delay value is greater than zero.
- Workflow seems stuck: Verify the delay isn't set to an unexpectedly large value.
- Downstream node errors on null: Provide an input or handle null outputs when no input is connected.
- Error about sleep value: Use a non-negative numeric value for the delay (e.g., 0 or greater).
