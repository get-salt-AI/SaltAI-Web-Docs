# JSON: To String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts any input data into a JSON-formatted string. Preserves numbers, booleans, lists, and objects; non-JSON-serializable values are coerced to their string representation. The Pretty option controls indentation for human-readable output.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsontostring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a JSON string representation of data to pass to APIs, store in text fields, or log. Typically follows nodes producing dictionaries/lists and precedes HTTP, file, or database nodes that accept text payloads.

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
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The data to convert to a JSON string. Can be a dict/object, list/array, number, boolean, null, or any other value (which will be converted to a string).</td><td style="word-wrap: break-word;">{'user': {'id': 123, 'name': 'Ada', 'roles': ['admin', 'editor']}}</td></tr>
<tr><td style="word-wrap: break-word;">pretty</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, formats the JSON with indentation for readability; if false, outputs compact JSON.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">json_string</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The JSON-formatted string representation of the input.</td><td style="word-wrap: break-word;">{"user":{"id":123,"name":"Ada","roles":["admin","editor"]}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- If the input is already a plain string, the output will be a JSON string containing that string (wrapped in quotes), not parsed JSON.
- Non-serializable objects are converted to their string representation; if you need structured JSON, supply serializable data (dicts, lists, numbers, booleans, null).
- Pretty formatting adds indentation and increases size; disable it for compact payloads in production or bandwidth-constrained contexts.
- Unicode characters are preserved (not ASCII-escaped) in the output string.
- This node does not validate or parse JSON strings; to parse a JSON string into structured data, use JSON: From String.

## Troubleshooting
- Output has unexpected surrounding quotes: This occurs when the input was a plain string. If you intended to parse JSON, use JSON: From String before converting.
- Output contains escaped characters (e.g., \" inside the text): This is normal for JSON string encoding. When downstream systems parse the JSON, escapes will resolve.
- Got a simple stringified object representation instead of structured JSON: The input contained non-serializable types. Convert them to dicts/lists or primitives before using this node.
- Output too large or hard to read: Toggle pretty to false for compact output, or true for readability as needed.
