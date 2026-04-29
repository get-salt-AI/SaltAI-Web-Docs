# Lazy Index Switch

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node routes exactly one of several alternative inputs downstream using an integer index selector. It supports up to ten inputs (value0-value9) of any type and evaluates them lazily: only the branch matching the selected index is actually requested or computed. This is useful for efficient branching where unused alternatives should not incur any compute cost.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltlazyindexswitch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Lazy Index Switch when you have multiple predefined options, such as different prompts, models, images, schedules, or parameter sets, and you need to choose exactly one at runtime using a numeric selector. It typically sits after nodes that define alternative branches (for example, multiple prompt builders, different image sources, or different configuration nodes) and before downstream consumers that operate on whichever variant is selected. Connect any compatible data type to value0..value9, then drive the index input from an upstream logic or control node (such as a numeric constant, a counter, or a value converted to an integer) to pick which branch is forwarded. This node works well alongside other logic nodes in the SALT/Logic category, like Lazy Switch or Lazy Conditional, when designing complex, efficient control flows. Place it in the pipeline where a single downstream path must receive one of several possible inputs while ensuring that non-selected branches are never computed. Because it uses lazy evaluation, it is especially helpful when each alternative branch is expensive, for example different large model calls or high-resolution image generations.

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
<tr><td style="word-wrap: break-word;">index</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based index indicating which value input to output. Valid range is 0-9 inclusive. If the chosen slot has no connected value, the node returns a null or empty output. Values outside this range are not allowed by the input constraints.</td><td style="word-wrap: break-word;">2</td></tr>
<tr><td style="word-wrap: break-word;">value0</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First candidate value (index 0). Accepts any data type, such as image, text, conditioning, or a model handle. Marked as lazy, so it is only evaluated if index = 0.</td><td style="word-wrap: break-word;">A base text prompt such as "Cinematic portrait of a medieval knight, 35mm, high detail"</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second candidate value (index 1). Same behavior as value0; only evaluated if index = 1.</td><td style="word-wrap: break-word;">An alternate text prompt such as "Futuristic city skyline at dusk, ultra wide angle"</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third candidate value (index 2). Only evaluated if index = 2.</td><td style="word-wrap: break-word;">An image tensor representing a 512x512 reference style image</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fourth candidate value (index 3). Only evaluated if index = 3.</td><td style="word-wrap: break-word;">A configuration object defining sampler type and number of steps</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fifth candidate value (index 4). Only evaluated if index = 4.</td><td style="word-wrap: break-word;">A model selection handle pointing to a specific checkpoint or LoRA variant</td></tr>
<tr><td style="word-wrap: break-word;">value5</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Sixth candidate value (index 5). Only evaluated if index = 5.</td><td style="word-wrap: break-word;">A text conditioning object combining prompt and negative prompt embeddings</td></tr>
<tr><td style="word-wrap: break-word;">value6</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Seventh candidate value (index 6). Only evaluated if index = 6.</td><td style="word-wrap: break-word;">An alternate reference image used only for some variants of the workflow</td></tr>
<tr><td style="word-wrap: break-word;">value7</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Eighth candidate value (index 7). Only evaluated if index = 7.</td><td style="word-wrap: break-word;">A different seed configuration for stochastic generation</td></tr>
<tr><td style="word-wrap: break-word;">value8</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Ninth candidate value (index 8). Only evaluated if index = 8.</td><td style="word-wrap: break-word;">A JSON-like structure with detailed rendering settings</td></tr>
<tr><td style="word-wrap: break-word;">value9</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Tenth candidate value (index 9). Only evaluated if index = 9.</td><td style="word-wrap: break-word;">An alternate routing or control configuration object</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The single selected value corresponding to the input at index valueN. The output type matches the type of the chosen input, such as image, text, or configuration. Downstream nodes should expect the same type they would have received if directly connected to that specific branch.</td><td style="word-wrap: break-word;">If index = 0 and value0 is a text prompt, the output will be that string. If index = 2 and value2 is an image tensor, the output will be that image tensor instead.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Because all value inputs are marked lazy, only the branch corresponding to the selected index is evaluated, which can greatly reduce compute when branches are expensive, such as multiple large model runs.
- **Limitations**: The index is constrained to 0-9. To support more than ten options, you must chain multiple Lazy Index Switch nodes or redesign your branching logic.
- **Behavior**: If the selected valueN input is not connected or resolves to null, the node returns a null or empty output for that run, which may cause downstream nodes to fail if they require a valid value.
- **Behavior**: The node does not perform type checking between branches. You must ensure that all possible selected values are compatible with the downstream nodes that consume this output.

## Troubleshooting
- **Selected branch returns nothing**: If the output is empty or downstream nodes report missing input, verify that the chosen valueN input, matching the current index, is connected and producing a non-null value.
- **Index seems to have no effect**: Check that the index input is within 0-9 and that no other node is overriding it. Confirm that any dynamic source driving the index produces an integer, not a float or string.
- **Type mismatch errors downstream**: If a downstream node fails with a type-related error, ensure that all branches that might be selected produce a compatible type for that consumer. Avoid mixing fundamentally different data types when the downstream node expects a specific one.
- **Unexpected resource usage**: If it appears that multiple heavy branches are being computed, confirm that the inputs are wired through lazy-capable nodes and that nothing forces evaluation before reaching the Lazy Index Switch.
