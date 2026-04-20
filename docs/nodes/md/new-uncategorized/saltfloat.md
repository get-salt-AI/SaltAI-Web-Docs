# Float

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node produces a single float (decimal) value that can be wired into any downstream node expecting a numeric input. It is a core primitive used to parameterize workflows with tunable numeric settings, thresholds, or coefficients. The value is configured directly on the node and passed through unchanged at execution time.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltfloat.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Float node whenever you need a configurable decimal number as part of your workflow logic, such as scores, weights, temperatures, probabilities, scaling factors, or any parameter that benefits from fractional values. It typically sits near the start or in configuration branches of a workflow, feeding into nodes that accept a FLOAT input, such as model configuration nodes, routing or logic nodes, or custom tool nodes. A common pattern is to pair Float with other primitives like SaltInteger, SaltString, and SaltBoolean to centralize all important parameters, then route these into downstream nodes such as SaltDeployedModel for model temperature or top_p, tool nodes for numeric arguments, or loop and logic nodes for numeric conditions. For best results, choose sensible defaults and keep Float nodes clearly labeled so they act as single sources of truth for numeric settings across the workflow.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The decimal number to output. Must be between -1000000000000000 and 1000000000000000 inclusive, with a step granularity of 0.01 in the UI. Values outside this range are not allowed and will be rejected by the editor. This is a literal constant: whatever you configure here is exactly what downstream nodes receive.</td><td style="word-wrap: break-word;">0.75</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The configured floating-point value, emitted as a primitive numeric output. Downstream nodes can consume this directly wherever a FLOAT is expected, such as a model temperature of 0.7, a similarity threshold of 0.85, or a scaling factor of 1.25. The output is a single scalar number, not a list or object.</td><td style="word-wrap: break-word;">0.85</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node is a constant value source and adds negligible overhead, so it is safe to use many Float nodes throughout a workflow.
- **Limitations**: The accepted numeric range is constrained to between -1000000000000000 and 1000000000000000; attempting to set values beyond this will not be allowed in the UI.
- **Behavior**: The node does not perform any computation or validation beyond the numeric range; it simply returns the configured literal float, so semantic correctness, such as using 0–1 versus 0–100, is your responsibility.
- **Behavior**: The float value is fixed at execution time based on the workflow configuration; it does not change dynamically unless the workflow is edited or re-run with a new configuration.

## Troubleshooting
- **Value cannot be set or slider stops changing**: If you cannot increase or decrease the value past a certain point, ensure the number stays within the allowed range of -1000000000000000 to 1000000000000000 and adjust to a smaller magnitude if needed.
- **Downstream node complains about invalid range, such as 'temperature must be between 0 and 1'**: Some consumers impose their own stricter ranges. Update the Float value to fit the downstream node's documented constraints, for example using 0.0 to 1.0 for temperature.
- **Unexpected integer-like behavior**: If you expected very fine-grained control but see only coarse changes, remember that the configured step size is 0.01. For extremely small adjustments, verify that this resolution is appropriate for the downstream node and adjust your expectations or logic accordingly.
