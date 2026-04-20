# Lazy Index Switch

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Lazy Index Switch chooses a single input from up to ten candidates (`value0`–`value9`) based on an integer `index`. Inputs are lazily evaluated, so only the selected branch is actually computed at runtime. This helps reduce unnecessary work in branching workflows where many heavy branches are wired but only one is needed per run.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltlazyindexswitch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use Lazy Index Switch when you need to pick exactly one value from several possible branches using a numeric index, while avoiding computation on the unused branches. Common scenarios include multi-mode pipelines (e.g., different render qualities, styles, or models), A/B/C testing between prompt or model variants, and routing to different post-processing chains. Place it where multiple upstream nodes provide candidate outputs to `value0`–`value9`, and a controller (such as a parameter node or configuration loader) sets the `index`. Connect its `value` output to downstream consumers that will operate on whichever option is selected. It pairs well with other logic nodes in the SALT/Logic category (e.g., "Lazy Switch", "Lazy Conditional", "Lazy Mix Images") to build more complex decision trees. As a best practice, keep all actively used `valueN` inputs type-compatible for the downstream chain, and ensure the `index` is within 0–9 and always points to a connected slot.

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
<tr><td style="word-wrap: break-word;">index</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based selector for which value input to output. Must be an integer between 0 and 9 inclusive. If the chosen index corresponds to an unconnected or invalid `valueN`, the node will effectively output no meaningful value.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">value0</td><td>True</td><td style="word-wrap: break-word;">WILDCARD (lazy)</td><td style="word-wrap: break-word;">Candidate value used when `index` is 0. Evaluated lazily, so it is only computed if selected. Can be any supported data type (image, text, embedding, configuration object, etc.) but must be compatible with downstream nodes.</td><td style="word-wrap: break-word;">A 512x512 draft image tensor from a fast render pipeline (mode 0).</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD (lazy)</td><td style="word-wrap: break-word;">Optional candidate value used when `index` is 1. Lazily evaluated and only runs when this branch is selected.</td><td style="word-wrap: break-word;">A 768x768 high-quality image tensor using more sampling steps (mode 1).</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD (lazy)</td><td style="word-wrap: break-word;">Optional candidate value used when `index` is 2. Only computed if this index is chosen.</td><td style="word-wrap: break-word;">A text prompt variant optimized for product photography (mode 2).</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD (lazy)</td><td style="word-wrap: break-word;">Optional candidate value used when `index` is 3.</td><td style="word-wrap: break-word;">An image result after a strong denoising and color-correction chain (mode 3).</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD (lazy)</td><td style="word-wrap: break-word;">Optional candidate value used when `index` is 4.</td><td style="word-wrap: break-word;">An embedding representing a specific cinematic style configuration (mode 4).</td></tr>
<tr><td style="word-wrap: break-word;">value5</td><td>False</td><td style="word-wrap: break-word;">WILDCARD (lazy)</td><td style="word-wrap: break-word;">Optional candidate value used when `index` is 5.</td><td style="word-wrap: break-word;">A low-resolution preview image tensor for fast iteration (mode 5).</td></tr>
<tr><td style="word-wrap: break-word;">value6</td><td>False</td><td style="word-wrap: break-word;">WILDCARD (lazy)</td><td style="word-wrap: break-word;">Optional candidate value used when `index` is 6.</td><td style="word-wrap: break-word;">A prompt variant focusing on macro close-up shots (mode 6).</td></tr>
<tr><td style="word-wrap: break-word;">value7</td><td>False</td><td style="word-wrap: break-word;">WILDCARD (lazy)</td><td style="word-wrap: break-word;">Optional candidate value used when `index` is 7.</td><td style="word-wrap: break-word;">A configuration object choosing an alternative model checkpoint (mode 7).</td></tr>
<tr><td style="word-wrap: break-word;">value8</td><td>False</td><td style="word-wrap: break-word;">WILDCARD (lazy)</td><td style="word-wrap: break-word;">Optional candidate value used when `index` is 8.</td><td style="word-wrap: break-word;">An upscaled image tensor prepared for final delivery (mode 8).</td></tr>
<tr><td style="word-wrap: break-word;">value9</td><td>False</td><td style="word-wrap: break-word;">WILDCARD (lazy)</td><td style="word-wrap: break-word;">Optional candidate value used when `index` is 9, the highest slot.</td><td style="word-wrap: break-word;">A heavily stylized artistic rendition of the base image using a complex style pipeline (mode 9).</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value selected from `value0`–`value9` corresponding to the current `index`. Its data type matches the chosen input (image, text, embedding, configuration object, etc.), so downstream nodes must be compatible with the types produced by all indices you plan to use.</td><td style="word-wrap: break-word;">If `index` is 1 and `value1` is a 768x768 image tensor, `value` will be that image tensor, ready for further processing or saving.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Only the branch that matches the current `index` is evaluated, which can significantly reduce compute and memory usage in graphs with many heavy branches.
- **Limitations**: The index range is fixed at 0–9; you cannot directly select more than ten branches with a single Lazy Index Switch instance.
- **Behavior**: If the selected `valueN` input is unconnected or resolves to None, the node will output a None-like value, which may cause downstream nodes to fail or skip processing.
- **Behavior**: All `valueN` inputs are wildcards; it is your responsibility to ensure all actively used branches produce a type and shape that downstream nodes can handle consistently.

## Troubleshooting
- **Output is None or downstream node fails**: Confirm that the `index` points to a connected `valueN` input and that the upstream branch for that slot produces a valid, non-None value.
- **Incorrect branch appears to be selected**: Check that you are using zero-based indexing (0 ↔ `value0`, 1 ↔ `value1`, etc.) and that the node or parameter driving `index` follows this convention.
- **Type mismatch or shape errors downstream**: Ensure all `valueN` branches you use return compatible data types (for example, all images if feeding an image-processing chain) and adjust any inconsistent upstream branches.
- **Need more than 10 options**: If you require more than 10 selectable modes, chain or combine multiple Lazy Index Switch nodes (for example, a first switch to choose a group, then another switch within that group) to cover additional branches.
