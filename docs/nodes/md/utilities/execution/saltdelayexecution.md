# Delay Execution

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltDelayExecution inserts a configurable time delay into the workflow before forwarding its input. It accepts any data type, waits for the specified number of seconds (including fractional values), and then returns that data unchanged. This allows you to control timing and pacing between steps without modifying the underlying data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/execution/saltdelayexecution.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to introduce an intentional pause in execution while preserving the exact data being passed through. Common scenarios include spacing out calls to external APIs to respect rate limits, allowing time for external systems to complete tasks such as writing files or updating records, or adding a short delay between generation steps for monitoring or manual checks. Place the node directly after the component that produces the data you wish to delay and before the component that consumes that data. Because it simply waits and passes its input forward, it can be easily combined with routing or control nodes to implement more sophisticated scheduling or staged processing.

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
<tr><td style="word-wrap: break-word;">delay</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The number of seconds to wait before emitting the output. Fractional values are supported for sub-second delays (for example, 0.5 for half a second or 0.01 for 10 ms). This value should be zero or positive; large values will produce a long blocking wait and delay downstream processing accordingly.</td><td style="word-wrap: break-word;">1.5</td></tr>
<tr><td style="word-wrap: break-word;">input</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Any data that should be passed through after the delay. This can be text, images, structured objects, or other supported types. The node does not inspect, validate, or modify this data; after the waiting period it is returned exactly as received. If left unconnected, the node still performs the delay and then outputs a None-equivalent value.</td><td style="word-wrap: break-word;">A generated response object containing model output text and metadata that should be forwarded to a logging or delivery node after a short pause.</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The same data that entered via `input`, emitted only after the specified delay has elapsed. The structure and type are identical to the input, allowing downstream nodes to treat it just as they would if connected directly to the original producing node.</td><td style="word-wrap: break-word;">The same response object with model output text and metadata, now available to a downstream logging or API delivery node after the configured delay.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs a blocking wait. During this time, any workflow branches that depend on this output will not advance, which may affect throughput when used with large delays or in high-frequency loops.
- **Limitations**: The node does not change or validate the data it forwards. Any incompatibilities or errors related to the data format will only surface when downstream nodes try to use the output.
- **Behavior**: If `input` is not provided, the node will still wait for the specified time and then output a None-equivalent value, which can cause errors if downstream nodes expect non-empty data.
- **Behavior**: The actual elapsed time may be slightly longer than requested due to scheduling and system load, but the delay will never be shorter than the configured value.

## Troubleshooting
- **Common Error 1**: The workflow appears to hang for a long time. Check the `delay` parameter for an unexpectedly large value (for example, 300 seconds). Adjust it to a more appropriate interval for your use case.
- **Common Error 2**: Downstream nodes receive `None` and fail. Ensure the `input` port of this node is wired to a valid upstream output and that the upstream node is successfully producing data.
- **Common Error 3**: Runtime errors about negative or invalid sleep duration. Confirm that `delay` is a non-negative numeric value and, if computed by prior nodes, that upstream logic cannot produce negative, NaN, or non-numeric outputs.
