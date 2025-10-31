# Lazy Switch

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Selects between two inputs based on a boolean switch, returning on_true when switch is True and on_false when False. Uses lazy evaluation so only the selected branch is evaluated and requested, helping avoid unnecessary computation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/SaltLazySwitch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to route data conditionally in a workflow. Connect a boolean decision to switch, and provide two alternative inputs for on_true and on_false. Only the chosen input is fetched/evaluated, making it ideal for expensive or optional branches.

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
<tr><td style="word-wrap: break-word;">switch</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean control that determines which input to return. True selects on_true, False selects on_false.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">on_false</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output when switch is False. Marked as lazy; it will not be evaluated unless needed.</td><td style="word-wrap: break-word;">Any compatible data type (e.g., an image tensor, text, number)</td></tr>
<tr><td style="word-wrap: break-word;">on_true</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output when switch is True. Marked as lazy; it will not be evaluated unless needed.</td><td style="word-wrap: break-word;">Any compatible data type (e.g., an image tensor, text, number)</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The selected value from on_true or on_false based on the switch.</td><td style="word-wrap: break-word;">The same type as the chosen input</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Both on_true and on_false should be of compatible types since the output type follows the selected input.
- Lazy evaluation means only the branch indicated by switch is requested and computed. The other branch can be unconnected or expensive without overhead.
- If an internal error occurs, the node may output a null/empty value. Ensure inputs are valid and available when selected.

## Troubleshooting
- Selected branch has no connection: If switch selects an unconnected input, the output will be empty/null. Connect the corresponding input or ensure the switch condition avoids it.
- Type mismatch downstream: Ensure consumers of value can handle the data type emitted by the selected branch.
- Unexpected computation on both branches: Verify you are not forcing both branches elsewhere; this node is lazy and only requests the selected input.
