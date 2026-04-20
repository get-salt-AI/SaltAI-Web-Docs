# Lazy Switch

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltLazySwitch routes either the `on_true` or `on_false` input to its output depending on a boolean `switch`. Both data inputs are marked as lazy, allowing the engine to only evaluate the branch that is actually selected. This makes it useful for conditionally enabling or disabling expensive parts of a workflow without paying their compute cost when they are not used.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltlazyswitch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltLazySwitch when you want a boolean-controlled choice between two alternative values or processing branches, and you want only the chosen branch to run. Common scenarios include toggling between a base render and an upscale chain, switching between two prompts or styles, or enabling/disabling an optional enhancement or safety step. Typically, a boolean parameter or logic node feeds into `switch`, while the outputs of two subgraphs (or simple values) feed into `on_false` and `on_true`. Place this node near the junction where the two branches rejoin the main pipeline, then connect its `value` output to the rest of your workflow (e.g., saving images, further processing, or feeding into other logic nodes such as "Lazy Conditional" or "Lazy Mix Images"). Ensure both branches produce compatible types, since downstream nodes see only the combined `value` output.

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
<tr><td style="word-wrap: break-word;">switch</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean control that decides which input is used. If true, the node selects `on_true`; if false, it selects `on_false`. This should come from a toggle, parameter control, or a logic/comparison node that outputs a boolean.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">on_false</td><td>True</td><td style="word-wrap: break-word;">WILDCARD (lazy)</td><td style="word-wrap: break-word;">The value or branch output used when `switch` is false. Declared as lazy, so if `switch` is true and this input is not needed elsewhere, its upstream computation can be skipped. Can carry any supported type (e.g., image, text, conditioning, numeric), but should match the type produced by `on_true` for safe downstream use.</td><td style="word-wrap: break-word;">An image from a 'Standard Render' chain used when an 'Enable Upscale' toggle is OFF.</td></tr>
<tr><td style="word-wrap: break-word;">on_true</td><td>True</td><td style="word-wrap: break-word;">WILDCARD (lazy)</td><td style="word-wrap: break-word;">The value or branch output used when `switch` is true. Also lazy, so when `switch` is false and this branch is not connected elsewhere, the engine can avoid computing it. Must be type-compatible with `on_false`, since downstream nodes will receive either one as a single `value` output.</td><td style="word-wrap: break-word;">An image from a 'High-Resolution Upscale' chain used when an 'Enable Upscale' toggle is ON.</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The selected value based on the boolean switch: `on_true` when `switch` is true, otherwise `on_false`. Its type is exactly whatever the chosen branch produces (image, text, conditioning, number, etc.), and it feeds directly into subsequent processing or saving nodes.</td><td style="word-wrap: break-word;">If `switch` is true and `on_true` is a 1024×1024 RGB image tensor from an upscale pipeline, `value` will be that upscaled image for use in a 'Save Image' node.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Because both `on_false` and `on_true` are lazy, only the branch selected by `switch` is expected to be evaluated, which can significantly reduce compute when one branch is much heavier than the other.
- **Limitations**: The wildcard type allows any data, but both branches should produce compatible types; sending an image on one side and a scalar on the other can cause downstream nodes to fail or behave unexpectedly.
- **Behavior**: If the active branch input is missing or fails upstream, the node may output a null/None value, and errors are logged rather than exposed as a dedicated error output.
- **Behavior**: Internally, the node can report which lazy input is required based on the switch state, enabling the engine to skip resolution of the unused lazy input, but this helper behavior is not exposed as additional ports in the UI.

## Troubleshooting
- **Output is null or missing**: If `value` appears empty, ensure the branch corresponding to the current `switch` value is actually connected and that its upstream nodes are producing valid data without errors.
- **Downstream type mismatch**: If a following node reports a type or shape error, confirm that both `on_true` and `on_false` deliver the same general type (e.g., both images or both text) so that the downstream node receives a predictable structure.
- **Switch has no visible effect**: If toggling `switch` does not change which branch is used, check that the boolean signal is correctly wired into the `switch` input and not into one of the value inputs, and verify that the boolean node is actually changing between true and false.
- **Both branches seem to run**: When you notice heavy compute on both branches, look for other nodes that may also consume `on_false` or `on_true` directly; any additional connections to a branch can force its evaluation even if it is not selected by the Lazy Switch.
