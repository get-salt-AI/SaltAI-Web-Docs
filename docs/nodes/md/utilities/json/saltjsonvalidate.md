# JSON: Validate

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Checks whether a given text is valid JSON. Returns a boolean indicating validity and an error message when invalid. Useful for gating flows before parsing or consuming JSON.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonvalidate.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you receive or construct JSON as text and need to confirm it is syntactically valid before further processing (e.g., extracting values, merging, or converting). Place it immediately after any step that outputs JSON text to prevent downstream failures.

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
<tr><td style="word-wrap: break-word;">json_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The JSON text to validate. Must be a JSON-formatted string (e.g., objects, arrays, numbers, booleans, null).</td><td style="word-wrap: break-word;">{"user": {"id": 123, "name": "Alex"}, "active": true}</td></tr>
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
<tr><td style="word-wrap: break-word;">error_message</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Empty string when valid; otherwise contains the parsing error message.</td><td style="word-wrap: break-word;">Expecting property name enclosed in double quotes: line 1 column 3 (char 2)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Validation scope**: This node validates JSON syntax only; it does not enforce schemas or field types.
- **Quoting rules**: JSON requires double quotes for keys and string values. Single quotes will cause validation errors.
- **Empty/whitespace input**: An empty or whitespace-only string is invalid JSON and will return an error.
- **No comments/trailing commas**: JSON does not allow comments or trailing commas; either will cause validation to fail.
- **Encoding**: Unicode characters are supported as long as the string is valid UTF-8.
- **Performance**: Extremely large JSON strings may take longer to validate.

## Troubleshooting
- **Validation unexpectedly fails**: Ensure keys and strings use double quotes and remove any trailing commas.
- **Received 'Unexpected error'**: Confirm the input is a proper string and not null/undefined. Recreate the JSON string or cast to text upstream.
- **Error mentions 'Expecting value' at start**: The input is empty or only whitespace. Provide a non-empty JSON string.
- **Error mentions 'Invalid control character'**: Escape special characters properly (e.g., newlines as \n within strings).
- **Error mentions 'Extra data'**: Ensure the input contains exactly one JSON value (no additional characters before/after the JSON).
