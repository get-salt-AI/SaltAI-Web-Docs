# String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node produces a single literal string value that can be reused anywhere in a workflow. It serves as a primitive text container, letting you define fixed prompts, labels, IDs, or other textual constants directly on the canvas. The node ensures a valid string is always emitted, substituting an empty string when no value is provided.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the String node whenever you need a constant or manually edited piece of text to flow through your workflow. Typical scenarios include defining a system prompt for a model, specifying a user ID or resource name, constructing parts of a URL, or providing labels, titles, and configuration flags as text. It usually appears near the start of a pipeline or close to the nodes that consume its value, such as model invocation nodes (e.g., SaltDeployedModel), tool nodes (SaltToolNode), or transformation nodes that format or parse strings.

You can connect its output to any input that expects a STRING type: prompt fields, configuration parameters, keys in an object builder, or items in a list. It is especially useful when combined with other primitive nodes like SaltInteger, SaltFloat, SaltBoolean, SaltList, and SaltObject to build structured configuration objects. Keep separate String nodes for logically distinct pieces of text (for example, one for system instructions and another for user-visible copy) to make workflows easier to read and maintain.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The literal text to output. This is free-form UTF-8 text, can be single-line or multi-line, and is entered directly in the node. There is no strict length limit at the node level, but very large strings may affect performance or downstream service limits. If left empty or set to null by upstream manipulation, the node will output an empty string.</td><td style="word-wrap: break-word;">You are a helpful assistant. Answer concisely and focus on actionable steps.</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The exact string value provided in the node, guaranteed to be a valid string (empty if no value was set). Downstream nodes can consume it wherever a STRING input is expected, such as prompts, labels, configuration fields, or components in templating and formatting nodes.</td><td style="word-wrap: break-word;">customer_support_summary_v1</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: While there is no hardcoded size limit, very large multi-kilobyte or megabyte strings (for example, entire documents) can make workflows heavier to load and edit; consider using file or document handling nodes for large content.
- **Limitations**: The node does not interpret or transform the text—no variable interpolation, templating, or automatic parsing is performed; it outputs exactly what you type (or what is passed in).
- **Behavior**: If the input value is ever passed as null by upstream logic, the node normalizes it to an empty string before output, avoiding type errors but potentially masking missing data issues.
- **Behavior**: This is a pure primitive node with no side effects or external calls; it is safe to duplicate and reuse across many parts of a workflow for shared constants like environment names, roles, or feature flags.

## Troubleshooting
- **Common Error 1**: Unexpected empty output — If downstream nodes receive an empty string, verify that the String node value field is filled and that no upstream manipulation is overwriting it with null or an empty value.
- **Common Error 2**: Downstream type mismatch — If a node refuses the connection or reports a type error, confirm that the target input expects STRING and not another type (such as INTEGER or OBJECT); use appropriate converter or builder nodes when a different type is required.
- **Common Error 3**: Prompt or text truncated by a model or tool — If long strings appear cut off in responses, the issue is usually a downstream service token or length limit, not this node; shorten the text or restructure the workflow to send only necessary segments.
- **Common Error 4**: Invisible whitespace issues — If behavior depends on exact text (for example, strict templates) and things fail subtly, check for leading or trailing spaces or unintended newlines in the String node value.
