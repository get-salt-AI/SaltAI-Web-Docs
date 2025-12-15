# Lazy Switch

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Selects between two inputs based on a boolean switch, returning only the chosen value. This node is lazy: it only requires and evaluates the input for the active branch, allowing the inactive branch to remain unconnected without triggering computation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltlazyswitch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to conditionally route one of two values (e.g., images, text, numbers, or other data) through your workflow based on a boolean flag. It is especially useful to avoid computing or loading heavy resources on the branch that will not be used.

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
<tr><td style="word-wrap: break-word;">switch</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls which input is forwarded. If true, outputs on_true; if false, outputs on_false.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">on_false</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output when switch is false. Can be any supported data type. Due to lazy behavior, this can be left unconnected if the switch will be true.</td><td style="word-wrap: break-word;">An image value or a text string like "Option A"</td></tr>
<tr><td style="word-wrap: break-word;">on_true</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output when switch is true. Can be any supported data type. Due to lazy behavior, this can be left unconnected if the switch will be false.</td><td style="word-wrap: break-word;">An image value or a text string like "Option B"</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The selected value based on the switch: on_true when switch is true, otherwise on_false.</td><td style="word-wrap: break-word;">The chosen image or string based on the switch</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Lazy evaluation**: Only the selected branch is required. You may leave the unselected branch unconnected without errors.
- **Type consistency**: The node does not convert types. Ensure downstream nodes can accept the type produced by the selected branch, and preferably keep both branches of compatible types.
- **Missing selected input**: If the selected branch is not provided, the node may output null.
- **Boolean control**: Non-boolean values are not accepted for the switch; provide a proper boolean input.

## Troubleshooting
- **Output is null**: Ensure the selected branch (based on switch) is connected and produces a value.
- **Downstream type mismatch**: Confirm both branches supply the same or compatible types expected by downstream nodes.
- **Unexpected branch evaluation**: Verify the switch boolean is set as intended and that any upstream logic correctly produces true/false.
- **Performance concerns**: If heavy computation is triggered unexpectedly, check that only the intended branch is selected; the inactive branch should not execute due to lazy behavior.
