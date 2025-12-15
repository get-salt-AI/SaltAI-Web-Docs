# JSON: From String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Parses a JSON-formatted string and returns the corresponding data structure. Supports objects, arrays, numbers, booleans, strings, and null. If the input is empty or invalid JSON, the node returns None.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonfromstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have JSON text (for example, from a file, API response, or a text field) and need to convert it into a data structure for downstream processing. Commonly paired with nodes like JSON: Get Value, JSON: Set Value, or JSON: Merge to manipulate the parsed data.

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
<tr><td style="word-wrap: break-word;">json_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A valid JSON string to parse. Supports multi-line input. Must be standard JSON (double-quoted strings, no comments, no trailing commas).</td><td style="word-wrap: break-word;">{"user": {"id": 123, "name": "Ada"}, "active": true, "tags": ["alpha", "beta"]}</td></tr>
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
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The parsed data structure from the JSON string. Can be an object (dict), array (list), string, number, boolean, or None if parsing fails or the input is empty/whitespace.</td><td style="word-wrap: break-word;">{'user': {'id': 123, 'name': 'Ada'}, 'active': True, 'tags': ['alpha', 'beta']}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Returns None on invalid or empty input**: If the string is not valid JSON or is empty/whitespace, the output will be None.
- **Standard JSON only**: Requires proper JSON formatting (double quotes for strings, no comments, no trailing commas).
- **Output type varies**: The WILDCARD output may be a dict, list, string, number, boolean, or None depending on the input.
- **Non-throwing behavior**: Errors are handled internally; the node logs errors and returns None instead of raising exceptions.

## Troubleshooting
- **Output is None**: Validate the input string with the JSON: Validate node, and check for common JSON issues (trailing commas, single quotes, comments).
- **Unexpected output type**: Confirm your JSON root type (object vs array vs scalar). Downstream nodes must handle the actual type returned.
- **Parsing works locally but fails here**: Ensure the input uses UTF-8 encoding and standard JSON (no BOM, no special comment syntax).
- **Downstream node errors**: If subsequent nodes fail, first check whether this node returned None due to invalid JSON.
