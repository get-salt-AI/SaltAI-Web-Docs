# Object

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node outputs a literal object (dictionary) value that you define directly in the workflow. It acts as a primitive key-value container for passing structured data between nodes, such as configuration, metadata, or model parameters. The node validates that its input is an object and returns a deep-copied version to avoid accidental mutation by downstream nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltobject.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Object node whenever you need to provide or transform structured, JSON-like data inside a workflow. Typical scenarios include: (1) defining configuration blocks like model settings, tool parameters, or feature flags that downstream nodes will read; (2) bundling related values (for example, user profile, preferences, and session info) into a single DICT; (3) shaping or standardizing payloads for nodes that call external services or run custom logic. It often appears near the start of a pipeline as a source of structured configuration, or mid-pipeline to regroup and override prior values into a clean object. Upstream, you usually configure its value inline in the UI or feed it from another DICT-producing node; downstream, it works well with any node expecting DICT inputs such as routing/branching nodes, tool or integration nodes, and custom processing nodes that inspect object fields. A common pattern is: primitive nodes (SaltInteger, SaltString, SaltBoolean, SaltList) and transformation nodes produce values, which are assembled into a DICT via this Object node, then consumed by model, agent, or tool nodes that rely on structured parameters.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">DICT</td><td style="word-wrap: break-word;">A key-value mapping representing the object to output. Keys should be strings, and values can be any JSON-like types supported by Salt (strings, numbers, booleans, lists, nested dicts, or structures built by other nodes). If this is not provided or is null, it defaults to an empty object {}. The node enforces that this input is a dictionary; any other type will result in a type error.</td><td style="word-wrap: break-word;">{"user": {"id": "user_42", "name": "Alex"}, "settings": {"theme": "dark", "notifications": true}, "session": {"expires_at": "2026-05-01T12:00:00Z"}}</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">DICT</td><td style="word-wrap: break-word;">The resulting object, returned as a DICT. This is a deep copy of the input so that downstream modifications do not affect the original configuration in this node. It can be passed into any node that accepts a DICT, including nodes that perform conditional logic, call tools or APIs, or template and format structured data.</td><td style="word-wrap: break-word;">{"user": {"id": "user_42", "name": "Alex"}, "settings": {"theme": "dark", "notifications": true}, "session": {"expires_at": "2026-05-01T12:00:00Z"}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node deep-copies the entire input object when executing; very large or deeply nested objects can increase memory usage and execution time.
- **Limitations**: The input value must be a dictionary-like object; passing a list, string, number, or other type will cause a type error and stop execution.
- **Behavior**: If the value input is omitted or explicitly set to null, the node will output an empty object {} instead of failing.
- **Behavior**: Because the output is a deep copy, any changes made by downstream nodes will not mutate the data stored in this node, helping to avoid shared-state side effects.

## Troubleshooting
- **Error: Object primitive expects a dict value.** This indicates value was set to a non-dict type (such as a string, list, or number). Fix it by ensuring the node is configured with an object/DICT and that any upstream connection also outputs a DICT.
- **Downstream node reports missing fields.** If another node cannot find expected keys, review the Object node’s value structure for typos or incorrect nesting (for example, settings.model vs. model.settings). Adjust keys and nesting to match what the downstream node expects.
- **Unexpected empty object output.** If the output is {}, check that you actually configured the value field and saved changes. An unset or cleared value will default to an empty object without raising an error.
