# While Loop Open (Deprecated)

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Opens a legacy while-style loop region. It emits a flow-control token and up to five pass-through values that act as the initial state for the loop. Actual looping is controlled by the corresponding While Loop Close node.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Logic/Flow Control/SaltWhileLoopOpen.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of a while-loop section. Provide a boolean condition and any initial values you want to carry through each loop iteration. Connect its outputs to the nodes inside the loop and then to a While Loop Close node that evaluates the condition and triggers the next iteration. Prefer the newer Loop Open/Close nodes for most workflows.

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
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Initial condition flag for the loop. The loop proceeds while the paired close node evaluates true.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">initial_value0</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First value carried into the loop (often used as a counter or primary state).</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">initial_value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second value carried into the loop.</td><td style="word-wrap: break-word;">state-a</td></tr>
<tr><td style="word-wrap: break-word;">initial_value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third value carried into the loop.</td><td style="word-wrap: break-word;">{'items': [1, 2, 3]}</td></tr>
<tr><td style="word-wrap: break-word;">initial_value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fourth value carried into the loop.</td><td style="word-wrap: break-word;">0.25</td></tr>
<tr><td style="word-wrap: break-word;">initial_value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fifth value carried into the loop.</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">FLOW_CONTROL</td><td style="word-wrap: break-word;">Flow-control token that must be connected to the matching While Loop Close node to form the loop.</td><td style="word-wrap: break-word;">Flow control handle</td></tr>
<tr><td style="word-wrap: break-word;">value0</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of initial_value0 for use inside the loop.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of initial_value1 for use inside the loop.</td><td style="word-wrap: break-word;">state-a</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of initial_value2 for use inside the loop.</td><td style="word-wrap: break-word;">{'items': [1, 2, 3]}</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of initial_value3 for use inside the loop.</td><td style="word-wrap: break-word;">0.25</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Pass-through of initial_value4 for use inside the loop.</td><td style="word-wrap: break-word;">False</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Deprecated**: This node is marked as deprecated. Prefer using Loop Open and Loop Close for new designs.
- **Pairing required**: Must be paired with While Loop Close to function. The close node controls iteration and condition evaluation.
- **State slots**: Provides exactly 5 state/value slots (value0–value4). Unused inputs will pass through as null.
- **Condition handling**: The condition is provided here but is evaluated by the close node each iteration.
- **Common pattern**: When used via For Loop wrappers, initial_value0 often holds the iteration counter.
- **Type agnostic**: All value slots are wildcard-typed and can carry any supported data type.

## Troubleshooting
- **Loop never iterates**: Ensure the FLOW_CONTROL output is connected to a While Loop Close node and that the close node's condition input is correctly wired.
- **Values reset or None inside loop**: Provide the corresponding initial_value inputs on the open node and ensure those outputs are connected through the loop into the close node.
- **Infinite loop or unexpected repeats**: Inspect the condition fed into the close node; it must eventually evaluate to false or update state each iteration.
- **Wrong data carried between iterations**: Verify that the loop close node routes updated values back into the open node's inputs for the next iteration.
- **Socket limit reached**: Only five value slots are available (0–4). Consolidate data into a structure if more are needed, or switch to the newer Loop nodes.
