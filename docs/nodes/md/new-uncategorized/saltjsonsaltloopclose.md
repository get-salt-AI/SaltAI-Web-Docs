# JSON Loop Close

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Finalizes a JSON-based loop that iterates over items from a JSON array. Works together with JSON Loop Open: it checks the loop’s continuation condition, signals completion, and returns the final values of any auxiliary data passed through the loop. When the loop ends (end of array or condition not met), FINISHED? outputs True.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltjsonsaltloopclose.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node with JSON Loop Open to iterate over a JSON array. Connect the LOOP output from JSON Loop Open to this node’s LOOP input. Set the condition to control whether the loop continues. Pass any auxiliary data (aux, aux2, aux3, aux4) through both nodes to maintain and retrieve state across iterations. Monitor FINISHED? to know when the loop has completed and then use the aux outputs for the final aggregated or updated values.

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
<tr><td style="word-wrap: break-word;">LOOP</td><td>True</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Loop context from JSON Loop Open. This establishes the linkage and state for the JSON iteration.</td><td style="word-wrap: break-word;">Connect from JSON Loop Open → LOOP</td></tr>
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Expression that determines if the loop should continue. If it evaluates to False, the loop ends and FINISHED? becomes True.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Primary auxiliary data passed through each iteration. Use it to carry and update state across the loop.</td><td style="word-wrap: break-word;">{"count": 3}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Secondary auxiliary data slot for additional state or payloads.</td><td style="word-wrap: break-word;">["accumulated", "values"]</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Tertiary auxiliary data slot for additional state or payloads.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Quaternary auxiliary data slot for additional state or payloads.</td><td style="word-wrap: break-word;">null</td></tr>
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
<tr><td style="word-wrap: break-word;">FINISHED?</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True when the loop has completed (array fully processed or condition evaluated to False).</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final value of the primary auxiliary data after the loop completes.</td><td style="word-wrap: break-word;">{"count": 10}</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final value of the secondary auxiliary data after completion.</td><td style="word-wrap: break-word;">["a", "b", "c"]</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final value of the tertiary auxiliary data after completion.</td><td style="word-wrap: break-word;">84</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Final value of the quaternary auxiliary data after completion.</td><td style="word-wrap: break-word;">null</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Condition controls continuation. If it evaluates to False, the loop stops and FINISHED? becomes True.
- Connect this node’s LOOP input to the LOOP output of JSON Loop Open. Without this connection, the node cannot operate.
- Aux inputs (aux–aux4) are optional passthroughs for maintaining state; their final values are emitted when the loop ends.
- An empty condition is treated as True.
- Hidden inputs are system-managed and typically should not be modified.

## Troubleshooting
- FINISHED? never becomes True: Ensure your condition will eventually evaluate to False or that the JSON array is non-empty and iterates to completion.
- Aux outputs are None: Verify that you wired aux/aux2/aux3/aux4 into both JSON Loop Open and JSON Loop Close throughout your loop.
- Loop stops immediately: Check the condition expression; if it evaluates to False on the first check, the loop will complete right away.
- Error about creating the JSON loop system: Update your Salt environment to the latest version to ensure loop orchestration features are supported.
- Unexpected condition behavior: Ensure any variables used in the condition are available from the loop context or provided as aux inputs.
