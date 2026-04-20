# Boolean

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltBoolean is a primitive node that outputs a single boolean value into the workflow. It lets you define a literal true/false flag that can be wired into conditions, branching logic, or parameter switches. The node is optimized for simple configuration with a toggle-style control and ensures the value is always a proper boolean.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltboolean.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltBoolean whenever you need a fixed true/false flag that can be reused across your workflow. Common scenarios include turning features or branches on and off (for example, enabling safety filters, choosing between two prompts, or toggling debug behavior), feeding conditions into control or branching nodes, or providing default flags into tools and model nodes that expect a BOOLEAN input. It typically appears early in a pipeline as a configuration primitive and is then connected downstream into logic or parameter nodes (for example: a conditional router node, a prompt-construction node that includes or omits sections based on a flag, or a tool node that has an enabled or strict_mode parameter). SaltBoolean works well together with other primitive nodes such as SaltInteger, SaltFloat, SaltString, SaltList, and SaltObject, forming a basic configuration layer for your workflow. A best practice is to centralize important workflow switches as SaltBoolean nodes, name them clearly (for example, Use strict moderation), and reuse their outputs wherever the same decision should apply, instead of duplicating boolean choices in multiple places.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">The literal boolean value this node should output. This is configured directly on the node via a toggle with labels "True" and "False". It accepts any truthy or falsy value internally but is always coerced to a strict boolean before being emitted.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">The boolean value produced by the node, guaranteed to be a strict true or false. Downstream nodes can consume this as a condition for branching, as a feature toggle, or as a parameter flag in tools, models, or other logic nodes.</td><td style="word-wrap: break-word;">false</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs only a simple boolean coercion and has negligible performance impact, so it can be used freely as a configuration primitive.
- **Limitations**: It always outputs a single scalar boolean; it cannot generate lists or objects of booleans or perform logical operations like AND or OR by itself.
- **Behavior**: Any input routed into the node's value parameter is coerced with standard truthiness rules, but the output is always a strict boolean (true or false).
- **Behavior**: The node is categorized under SALT/Core/Primitives and is intended primarily as a stable, reusable configuration flag rather than a dynamic computation node.

## Troubleshooting
- **Boolean seems inverted**: If downstream behavior looks inverted (for example, a branch runs when it should not), double-check both the value toggle on the SaltBoolean node and how the downstream node interprets the flag; some use a field like enabled = true, others might use an inverse meaning such as skip_when_true.
- **Cannot connect output**: If you cannot connect the output to another node, verify that the target input is of type BOOLEAN; many numeric or string parameters will not accept a boolean connection.
- **Unexpected truthiness**: If you are feeding a non-boolean into the value parameter (for example from a script or custom node), remember it will be coerced to a boolean; ensure the upstream node returns an actual boolean if you need precise control.
