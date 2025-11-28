# JSON: To String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts any input data into a JSON-formatted string. Supports pretty (indented) or compact formatting and safely handles common Python types, converting non-JSON-serializable values to strings. Returns a single STRING output containing the serialized JSON.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsontostring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to serialize data for logging, storage, display, or passing into downstream nodes or external services that expect JSON text. Common in workflows that transform structured data (dicts/lists) into a transportable string or when preparing payloads for API requests.

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
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The data to convert to a JSON string. Accepts dictionaries, lists, numbers, booleans, None, strings, and other types (which will be stringified).</td><td style="word-wrap: break-word;">{'user': {'id': 123, 'name': 'Ava'}, 'items': [{'sku': 'A-100', 'qty': 2}]}</td></tr>
<tr><td style="word-wrap: break-word;">pretty</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, output is indented for readability; if false, output is compact with no extra whitespace.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">json_string</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The JSON-formatted string representation of the input data.</td><td style="word-wrap: break-word;">{   "user": {     "id": 123,     "name": "Ava"   },   "items": [     {       "sku": "A-100",       "qty": 2     }   ] }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Strings as input are converted to JSON string literals (they will be wrapped in quotes and may contain escaped characters), not passed through verbatim.
- Non-JSON-serializable objects are converted to their string representation.
- None becomes null; booleans are rendered as true/false; Unicode is preserved (no ASCII escaping).
- Pretty formatting adds indentation; compact formatting uses minimal separators.
- On serialization errors, the node falls back to returning str(input), which may not be valid JSON.

## Troubleshooting
- Output shows extra quotes or escaped characters: Your input was a plain string; the node emits a JSON string literal. If you need the raw string, avoid converting to JSON or parse downstream accordingly.
- Output is 'null' or empty-like: The input might have been None or an empty/whitespace string parsed as None upstream.
- Output is not valid JSON: The node fell back to str(input) due to a serialization error. Check for circular references or unsupported objects and consider converting them to dict/list or primitives before serialization.
- Unexpected large file size: Disable pretty formatting (set pretty to false) to produce a compact JSON string.
