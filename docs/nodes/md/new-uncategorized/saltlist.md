# List

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node produces a LIST primitive that can be used as structured data in a workflow. It takes an input list and returns a deep-copied version to avoid side effects from downstream modifications. It is a foundational primitive for passing ordered collections, such as items, messages, or records, between nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltlist.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the List node whenever you need to introduce or manipulate an ordered collection of values as data within a workflow. Typical scenarios include: (1) providing a fixed list of items (for example, user names, messages, or IDs) as input to processing nodes, (2) feeding a list of context strings into LLM nodes, and (3) standardizing list outputs from earlier steps before passing them further downstream. In a typical pipeline, the List node appears early as a data source or in the middle as a normalizer: upstream you might have manual configuration or values from primitive nodes like Text or Number, and downstream you might connect to nodes such as SaltLLM (for batched or contextual prompts) or other collection-processing nodes (filters, mappers, aggregators). An important integration pattern is using List to explicitly declare that an input is literal list data, ensuring the execution engine does not misinterpret list-shaped inputs (such as ["1", 0]) as node connections. Best practice: validate that your list contains only data types that downstream nodes accept (for example, strings for LLM context) and, when referencing node outputs, pass the connection itself via the workflow wiring rather than embedding connection-like structures inside this list.

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
<tr><td style="word-wrap: break-word;">value</td><td>False</td><td style="word-wrap: break-word;">LIST</td><td style="word-wrap: break-word;">The literal list value to output. Must be a JSON-style list or array (for example, ["item1", "item2"]). If omitted or set to null, the node outputs an empty list. The value is treated strictly as data: it cannot be a connection spec and must be a genuine list object; otherwise, a type error is raised.</td><td style="word-wrap: break-word;">["John", "Kate", "Maria"]</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">LIST</td><td style="word-wrap: break-word;">A deep-copied LIST containing the items from the input. Downstream nodes receive a fresh list instance so that any modifications they make do not affect the original value or other branches of the workflow. The structure is a plain ordered collection, preserving item order and element types.</td><td style="word-wrap: break-word;">["John", "Kate"]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node deep-copies the list before outputting it. For very large lists or deeply nested structures, this can add overhead; consider this if you are passing large datasets.
- **Limitations**: The input must be a proper list value. Passing other types (such as a string, dictionary, or scalar) will result in a type error rather than implicit conversion.
- **Behavior**: If the input value is omitted or explicitly set to null, the node outputs an empty list [], making it safe to use as a default list initializer.
- **Behavior**: The list is always treated as data. Even if it contains elements that look like node connections (for example, ["1", 0]), they are not resolved as references but passed through literally.

## Troubleshooting
- **Common Error 1**: "List primitive expects a list value." — This occurs when the value input is not a list (for example, a string or number). Fix by wrapping your items in a list, such as using ["John", "Kate"] instead of "John".
- **Common Error 2**: Downstream node fails due to unexpected item types — Some consumers (like LLM context inputs) expect all list elements to be strings. Ensure your value list contains only compatible types (for example, convert numbers or objects to strings before passing).
- **Common Error 3**: Workflow seems to ignore connection-like patterns in the list — If you include items like ["1", 0] inside value, they are treated as plain data, not connections. To use another node's output, connect that node directly via the workflow editor instead of embedding connection descriptors inside this list.
