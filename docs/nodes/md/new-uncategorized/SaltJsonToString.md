# JSON: To String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts any input data into a JSON-formatted string. Supports pretty (indented) or compact output and handles common Python types like dicts, lists, numbers, booleans, and nulls.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/SaltJsonToString.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a clean JSON string from data produced elsewhere in your workflow—such as preparing payloads for APIs, logging structured output, or storing configuration/results as text. Toggle Pretty for human-readable formatting or disable it for compact single-line output.

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
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The data to convert to JSON. Accepts objects, arrays, numbers, booleans, null, or strings.</td><td style="word-wrap: break-word;">{'user': {'id': 123, 'name': 'Ada'}, 'active': True}</td></tr>
<tr><td style="word-wrap: break-word;">pretty</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, formats the JSON with indentation for readability; if false, produces compact JSON.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">json_string</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The JSON-formatted string representation of the input.</td><td style="word-wrap: break-word;">{   "user": {     "id": 123,     "name": "Ada"   },   "active": true }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Strings as input**: If the input is already a string, the output will be a valid JSON string with quotes (e.g., input hello becomes "hello").
- **Fallback on errors**: If serialization fails, the node returns the input's plain string representation, which may not be valid JSON.
- **Compact vs pretty**: Set Pretty to false for compact single-line JSON when minimizing size matters (e.g., API payloads).
- **Type handling**: Common data types are serialized to JSON equivalents; values not naturally JSON-serializable are converted to their string form.

## Troubleshooting
- **Output is not valid JSON**: Ensure the input is JSON-serializable. If errors occurred, the node may have fallen back to a plain string; validate with a JSON validator.
- **Unexpected quotes around output**: This occurs when the input is a raw string. If you already have JSON text and don't want it re-quoted, parse it first, then convert.
- **Missing indentation**: Set Pretty to true to enable multi-line, indented formatting.
- **Unicode characters escaped unexpectedly**: Check downstream consumers; this node preserves non-ASCII characters in the output, but other steps might alter encoding.
