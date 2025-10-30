# Delay Execution

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Pauses the current branch of a workflow for a specified number of seconds, then forwards the provided data unchanged. Supports fractional seconds for sub-second waits. Useful for pacing, synchronization, or rate-limiting steps.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/SaltDelayExecution.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Insert this node between two steps when you need to wait before continuing (e.g., spacing out API calls, giving time for external processes, or coordinating concurrent branches). Set the delay in seconds; connect any upstream data to have it forwarded after the wait.

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
<tr><td style="word-wrap: break-word;">delay</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Number of seconds to wait before forwarding data. Supports fractional values for sub-second delays.</td><td style="word-wrap: break-word;">2.5</td></tr>
<tr><td style="word-wrap: break-word;">input</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Any data to pass through after the delay. The node does not modify this data.</td><td style="word-wrap: break-word;">Upstream data object to be delayed (e.g., an image, text, or structured payload)</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The same data that was provided to the input, emitted after the delay completes.</td><td style="word-wrap: break-word;">Same object/value received at input</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node blocks its downstream execution for the specified duration; the rest of the workflow branch resumes only after the delay completes.
- Fractional seconds are supported (e.g., 0.1 for 100 ms). Actual timing precision depends on the operating system and system load.
- Providing a negative delay will cause an error from the underlying timing system.
- If no input is connected, the node will output an empty/null value after the delay.
- Long delays will increase total workflow runtime; use with care in time-sensitive pipelines.

## Troubleshooting
- Node seems stuck or slow: It is likely waiting for the configured delay. Reduce the delay value to speed up execution.
- No output received: Ensure the input is connected. Without an input, the node forwards a null/empty output after waiting.
- Error about delay value: Use a non-negative float for the delay (e.g., 0, 0.5, 3). Negative values are not allowed.
- Downstream steps start too early: Place this node directly before the steps you need to pause to ensure they are gated by the delay.
