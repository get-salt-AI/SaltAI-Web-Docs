# JSON: From String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Parses a JSON-formatted string and outputs the corresponding data structure. If the input is not valid JSON or is empty/whitespace, the node returns None. The output type is flexible (object, array, string, number, boolean, or null) depending on the parsed content.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/SaltJsonFromString.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to convert a JSON string (from a file, API response, prompt output, or text input) into structured data for downstream processing. Commonly paired with nodes that read, query, or modify JSON data such as JSON: Get Value, JSON: Set Value, JSON: Merge, or JSON: Filter.

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
<tr><td style="word-wrap: break-word;">json_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The JSON string to parse. Supports multiline text. Must be valid JSON to return structured data.</td><td style="word-wrap: break-word;">{ "user": { "name": "Ada", "id": 42 }, "active": true }</td></tr>
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
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The parsed data structure from the input string. Can be an object (dict), array (list), string, number, boolean, or null. Returns None if parsing fails.</td><td style="word-wrap: break-word;">{'user': {'name': 'Ada', 'id': 42}, 'active': True}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- If the input string is invalid JSON or empty, the node returns None.
- Output type varies based on input JSON (object, array, string, number, boolean, or null). Ensure downstream nodes can handle None.
- This node does not attempt to fix malformed JSON; validate or correct your string upstream if needed.
- Multiline input is supported, which is helpful for large or pretty-printed JSON.

## Troubleshooting
- Parsed output is None: The input is likely invalid JSON or empty. Validate with the JSON: Validate node or check for syntax errors (missing commas/braces, unquoted keys, etc.).
- Downstream node errors: Ensure downstream nodes can handle None and that you provide a valid JSON string to this node.
- Unexpected output type: The output mirrors the JSON content. Confirm the input string matches the structure your workflow expects (e.g., array vs. object).
- Large or complex JSON not parsing: Check for hidden control characters or encoding issues; ensure the input is UTF-8 and properly escaped.
