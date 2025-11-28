# Lazy Switch

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Selects and forwards one of two inputs based on a boolean switch, while lazily requesting only the branch that’s actually needed. This helps avoid computing or loading the non-selected branch.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltlazyswitch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to gate between two alternative values (e.g., models, prompts, images, parameters) based on a boolean condition. Connect heavy or expensive nodes to on_true/on_false so only the chosen branch is evaluated when the switch decision is made.

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
<tr><td style="word-wrap: break-word;">switch</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Condition that determines which input to forward. True selects on_true; False selects on_false.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">on_false</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output when switch is False. Marked lazy: it may remain uncomputed/unconnected until selected.</td><td style="word-wrap: break-word;">a text prompt value</td></tr>
<tr><td style="word-wrap: break-word;">on_true</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to output when switch is True. Marked lazy: it may remain uncomputed/unconnected until selected.</td><td style="word-wrap: break-word;">an image tensor</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Pass-through of the selected input (on_true if switch is True, otherwise on_false). Type matches the selected branch.</td><td style="word-wrap: break-word;">the chosen prompt/image/value</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node is lazy: it requests upstream computation only for the branch required by the current switch value.
- Both on_true and on_false should resolve to the same kind of data to ensure downstream compatibility.
- If the selected branch is not connected or cannot be produced, the node will request it; unresolved inputs will cause execution to wait or fail upstream.
- The node does not alter or coerce types; it simply forwards the chosen input.

## Troubleshooting
- Selected branch not connected: Ensure the branch corresponding to the current switch value is connected or can be produced when requested.
- Downstream type errors: Make sure on_true and on_false produce the same type/shape expected by downstream nodes.
- Unexpected None output: Check upstream nodes for errors on the selected branch and confirm they can produce a value when requested.
- Performance not improving: Verify heavy computations are placed behind the on_true/on_false connections so they are only triggered when that branch is selected.
