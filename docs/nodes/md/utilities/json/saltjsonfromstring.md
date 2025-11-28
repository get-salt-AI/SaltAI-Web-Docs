# JSON: From String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Parses a JSON string and outputs the corresponding structured data. The output type adapts to the JSON content (object, array, number, string, boolean, or null). If the input is empty or invalid JSON, the node returns None.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonfromstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have JSON as text (for example, from an API response, file content, or a prompt result) and need to convert it into structured data for downstream processing. Commonly paired with JSON: Get Value, JSON: Filter, JSON: Merge, or JSON: To String.

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
<tr><td style="word-wrap: break-word;">json_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A valid JSON string to parse. Must follow standard JSON syntax (double-quoted keys/strings, no trailing commas, no comments).</td><td style="word-wrap: break-word;">{"user": {"id": 123, "name": "Ada"}, "tags": ["ml", "ai"]}</td></tr>
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
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Parsed data from the JSON string. Can be an object (dict), array (list), number, string, boolean, or None if parsing fails.</td><td style="word-wrap: break-word;">{'user': {'id': 123, 'name': 'Ada'}, 'tags': ['ml', 'ai']}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Valid JSON required**: The input must be valid JSON (double quotes, no comments, no trailing commas).
- **Empty or invalid input returns None**: Blank strings or malformed JSON yield None, which may break downstream nodes expecting structured data.
- **Dynamic output type**: The output type depends on the JSON root (object, array, etc.). Ensure downstream nodes can accept WILDCARD.
- **No reformatting**: This node only parses; it does not pretty-print. Use JSON: To String to serialize back to text if needed.
- **Strings vs JSON**: A plain string like "hello" is valid JSON and will output a string value, not an object.

## Troubleshooting
- **Got None as output**: The JSON is likely invalid or empty. Validate with JSON: Validate and fix syntax issues (e.g., replace single with double quotes, remove trailing commas).
- **Downstream node type mismatch**: Ensure downstream nodes accept WILDCARD or insert JSON: To String if text is required.
- **Unexpected list vs object**: If you expected an object but got a list (or vice versa), check the top-level JSON structure in the input.
- **Unicode/encoding issues**: Ensure the source text is UTF-8 and not truncated. Re-fetch or re-read the JSON source if corruption is suspected.
