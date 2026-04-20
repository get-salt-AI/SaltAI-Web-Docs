# Integer

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node produces a single, literal integer value that can be wired into other nodes as a primitive input. It is typically used to configure numeric parameters, loop counters, thresholds, and other whole-number settings within a workflow.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltinteger.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Integer node whenever you need a fixed whole-number value inside a workflow, such as setting a retry limit, pagination index, maximum items to process, or other discrete configuration parameters. It generally appears in configuration or setup sections of a pipeline as a source of constants that downstream nodes consume. Typical downstream consumers include nodes that accept INT parameters (for example, a max_results field, a top_k parameter, or a loop bound), as well as conditional or control-flow nodes that branch based on numeric values. It works well alongside other primitive nodes like SaltBoolean, SaltFloat, SaltString, SaltList, and SaltObject when assembling structured configuration objects. Best practice is to keep frequently reused numeric constants in dedicated Integer nodes so changes can be made centrally, and to stay within the allowed range to avoid validation errors.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The literal integer this node will output. Must be a whole number between -2147483648 and 2147483647 inclusive. Decimals are not allowed and will be rejected; the value is intended for stable numeric configuration such as limits, counters, or offsets.</td><td style="word-wrap: break-word;">25</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The configured integer value, emitted as a primitive INT. Downstream nodes can use it directly for numeric parameters, calculations, loop bounds, indexing, or any field that expects an integer. The value is passed through exactly as configured.</td><td style="word-wrap: break-word;">25</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node has negligible performance impact; it simply returns a configured integer and can be used freely throughout workflows.
- **Limitations**: Only 32-bit signed integers are allowed (from -2147483648 to 2147483647); values outside this range cannot be entered or saved.
- **Behavior**: The node is stateless and deterministic; once configured, it always returns the same integer until manually changed.
- **Behavior**: This node does not take dynamic inputs; to change the integer at runtime, you must either adjust this node's configuration or generate an INT from another node and connect that node directly to consumers.

## Troubleshooting
- **Common Error 1**: Value cannot be saved or shows validation warning — Ensure the value is within the -2147483648 to 2147483647 range and is a whole number without decimals.
- **Common Error 2**: Downstream node rejects the connection — Confirm the target field expects an INT, not a FLOAT or STRING, and that you are connecting from the Integer node's value output.
- **Common Error 3**: Changing the integer does not affect workflow results — Check that the correct Integer node is wired into the parameter in question and that no other node or default setting is overriding that value.
