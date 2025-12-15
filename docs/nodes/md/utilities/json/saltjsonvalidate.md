# JSON: Validate

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Checks whether a given string is valid JSON. Returns a boolean indicating validity and an error message string describing why validation failed, or empty when valid.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonvalidate.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to verify that text produced by an upstream step is well-formed JSON before attempting to parse or consume it. Commonly placed after a model or tool that outputs JSON-like text to gate downstream processing.

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
<tr><td style="word-wrap: break-word;">json_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text to validate as JSON. Must be a standard-compliant JSON string (double-quoted keys/strings, no comments or trailing commas).</td><td style="word-wrap: break-word;">{"user": {"id": 123, "name": "Ada"}, "items": [1, 2, 3]}</td></tr>
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
<tr><td style="word-wrap: break-word;">is_valid</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True if the input is valid JSON, otherwise False.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">error_message</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">An error description if validation fails. Empty string when the JSON is valid.</td><td style="word-wrap: break-word;">Expecting property name enclosed in double quotes: line 1 column 3 (char 2)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Validation only**: This node does not return parsed data; it only checks validity.
- **Strict JSON**: Only standard JSON is accepted (no comments, single quotes, or trailing commas).
- **Input type matters**: Non-string inputs (e.g., None) will produce an 'Unexpected error' message; ensure you pass a string.
- **Empty output on success**: The error_message output is an empty string when the JSON is valid.

## Troubleshooting
- **Invalid due to quotes**: Use double quotes for keys and string values (e.g., {"key": "value"}), not single quotes.
- **Trailing comma error**: Remove any trailing commas in objects or arrays.
- **Escape characters**: Ensure special characters inside strings are properly escaped (e.g., newline as \n).
- **Non-string input**: If you see 'Unexpected error', verify the input is a STRING and not null or another type.
- **Large payloads**: If validation intermittently fails, check for truncated input from upstream nodes and ensure the full JSON string is passed.
