# JSON: From String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Parses a JSON string and returns the corresponding data structure. If the input is not valid JSON, the node returns None. Supports multiline input for large or formatted JSON blocks.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonfromstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have JSON text (e.g., from an API response, file, or another node that outputs a string) and need to convert it into structured data for downstream processing. Typically paired after nodes that fetch or construct JSON strings, and before nodes that inspect, filter, or extract values from JSON.

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
<tr><td style="word-wrap: break-word;">json_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The JSON text to parse into a data structure. Must be valid JSON with proper quoting and structure.</td><td style="word-wrap: break-word;">{"name": "Ada", "skills": ["math", "logic"], "active": true}</td></tr>
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
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The parsed data structure. Can be an object (dict), array (list), string, number, boolean, null, or None if parsing fails.</td><td style="word-wrap: break-word;">{'name': 'Ada', 'skills': ['math', 'logic'], 'active': True}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Returns None on invalid JSON**: If parsing fails, the output is None instead of raising an error.
- **Multiline supported**: The input field supports multiline JSON strings for readability.
- **Exact JSON required**: Use double quotes for keys/strings and avoid trailing commas or comments.
- **Data type passthrough**: Output type varies based on the root JSON type (object, array, string, number, boolean, null).
- **Large inputs**: Very large JSON strings may impact performance; consider validating or streaming upstream.

## Troubleshooting
- **Output is None**: The JSON is invalid. Ensure proper JSON formatting (double quotes, no trailing commas, valid escapes).
- **Unexpected data type**: Check the top-level JSON value. If it's an array, the output will be a list; if it's an object, a dict, etc.
- **Encoding issues**: Make sure the input string is UTF-8 and special characters are properly escaped (e.g., "\n", "\uXXXX").
- **Boolean or null errors**: JSON uses true/false/null (lowercase), not True/False/None.
