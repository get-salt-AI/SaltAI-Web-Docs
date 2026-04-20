# JSON: To String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node converts arbitrary input data (numbers, text, lists, dicts, or other structures) into a JSON-formatted string. It uses a standard serializer so common types become valid JSON, and all other objects are converted to strings before encoding. A Pretty option lets you choose between compact output or human-readable, indented JSON.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsontostring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to turn structured data into JSON text for logging, storage, or sending to external systems such as webhooks and REST APIs. It typically follows nodes that assemble or transform data structures (for example, JSON utility nodes that build dicts or lists) and precedes nodes that require a text payload, such as HTTP request or file-writing nodes. A common pattern is to first construct or modify structured data using nodes like "SaltJsonSetValue" and "SaltJsonMerge", then pass the result to "SaltJsonToString" before delivering it to an integration or saving it. During development or debugging, enable the Pretty option for readability in logs; for production or network-sensitive pipelines, disable Pretty to reduce payload size and bandwidth usage.

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
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The data to convert to JSON text. Accepts any type: dicts and lists are emitted as structured JSON, primitives (strings, integers, floats, booleans) are serialized directly, and None becomes null. Custom or unsupported object types are converted to their string representation before JSON encoding. Large or deeply nested structures produce proportionally large JSON strings, so pass only the data you actually need to serialize.</td><td style="word-wrap: break-word;">{'user': {'id': 12345, 'name': 'Amina Ortega', 'email': 'amina@example.com', 'preferences': {'language': 'en', 'notifications': True}}, 'request_id': 'req-8f92c1e7', 'items': [{'sku': 'SKU-001', 'quantity': 2, 'price': 19.99}, {'sku': 'SKU-002', 'quantity': 1, 'price': 5.5}]}</td></tr>
<tr><td style="word-wrap: break-word;">pretty</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls whether the JSON output is formatted with indentation and line breaks. When true, the JSON string is human-readable and easier to inspect; when false, the output is compact with minimal whitespace, which is better for size- or bandwidth-sensitive use cases.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">json_string</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted string representation of the input data. With pretty set to true, the string is multi-line and indented; with pretty set to false, it is compact and typically one line. Downstream nodes can treat this as plain text for logging, templating, or embedding in messages, or as a JSON payload for integrations such as HTTP POST bodies.</td><td style="word-wrap: break-word;">{"user":{"id":12345,"name":"Amina Ortega","email":"amina@example.com","preferences":{"language":"en","notifications":true}},"request_id":"req-8f92c1e7","items":[{"sku":"SKU-001","quantity":2,"price":19.99},{"sku":"SKU-002","quantity":1,"price":5.5}]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Serialization cost grows with input size and nesting depth; avoid passing unnecessary large or raw datasets to keep resource usage reasonable.
- **Limitations**: Custom objects or non-JSON-native types are converted to strings, which may not be directly interpretable as structured JSON by downstream systems expecting richer types.
- **Behavior**: If an internal error occurs during serialization, the node logs the error and falls back to using the plain string representation of the input, which can result in output that is not valid JSON.
- **Behavior**: Standard JSON conventions are followed for primitive values: booleans become true/false, None becomes null, and numeric values remain numeric instead of being quoted as strings.

## Troubleshooting
- **Output is not valid JSON for a downstream consumer**: This can happen if a serialization error occurred and the node fell back to str(input). Check logs near this node, simplify or sanitize the input, and convert custom objects into plain dicts or lists before passing them in.
- **Complex values appear as plain or escaped strings**: When nested or custom objects show up as strings, they were not JSON-serializable. Normalize or transform such objects upstream into JSON-friendly structures (dicts, lists, primitives) before using this node.
- **Very large or slow outputs**: If you see performance issues or very large JSON strings, confirm you are not sending oversized or unfiltered structures. Trim, filter, or summarize data upstream and consider disabling Pretty to reduce size.
- **External service reacts badly to formatted JSON**: Some systems are sensitive to unexpected whitespace or line breaks. If you encounter odd behavior with pretty-printed JSON, turn off the Pretty option to generate compact JSON and retry.
