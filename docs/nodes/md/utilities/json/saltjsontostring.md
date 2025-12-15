# JSON: To String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts any input data into a JSON-formatted string. Supports pretty-printing with indentation for readability or compact output for efficient storage/transmission. Handles diverse data types and ensures a consistent JSON representation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsontostring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to serialize data (objects, arrays, numbers, booleans, etc.) into a JSON string for logging, storage, configuration export, or passing into downstream nodes that expect a string payload (e.g., HTTP requests, file writers, or prompt builders). Typically placed after data assembly/transformation nodes and before output or transport nodes.

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
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The data to convert to a JSON string. Accepts objects/dicts, arrays/lists, strings, numbers, booleans, null, or other values.</td><td style="word-wrap: break-word;">{'user': {'id': 123, 'name': 'Ada'}, 'active': True, 'roles': ['admin', 'editor']}</td></tr>
<tr><td style="word-wrap: break-word;">pretty</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, formats the JSON with indentation for readability. If false, outputs a compact JSON string without extra spaces.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">json_string</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The JSON-formatted string representation of the input.</td><td style="word-wrap: break-word;">{   "user": {     "id": 123,     "name": "Ada"   },   "active": true,   "roles": [     "admin",     "editor"   ] }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Strings are quoted**: If the input is already a string, the output will be a JSON string (quoted and escaped). This is correct JSON behavior.
- **Pretty vs compact**: Setting pretty to false produces compact output without spaces, suitable for storage or network transfer.
- **Unicode preserved**: Non-ASCII characters are preserved (not escaped) in the output.
- **Type handling**: Numbers and booleans are emitted as standard JSON literals (e.g., true/false). Null becomes null.
- **Fallback for complex objects**: If an input value cannot be natively serialized to JSON, it is converted to its string representation.
- **Wildcard input**: The node accepts any data type; ensure the resulting JSON structure matches downstream expectations.

## Troubleshooting
- **Output shows extra quotes around my text**: You likely passed a plain string as input. JSON strings are quoted by design. If you need raw text, skip JSON serialization or remove quotes downstream.
- **Output is a single-line, compact string**: Set pretty to true to enable indentation and line breaks for readability.
- **Downstream node rejects the payload**: Confirm the consumer expects a JSON string and that the structure matches its schema. If you intended structured data (not a string), use a node that outputs objects instead.
- **Unexpected values for custom objects**: Complex or unsupported objects are stringified. Convert them to dictionaries/lists first or extract the required fields before serialization.
- **Non-English characters appear garbled**: The node preserves Unicode. If you see garbling, check the display font/encoding of the viewer or the downstream system, not this node.
