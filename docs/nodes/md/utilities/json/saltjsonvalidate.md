# JSON: Validate

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Checks whether a given text is valid JSON. Returns a boolean indicating validity and an accompanying error message if invalid. Does not modify or parse the input beyond validation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonvalidate.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to verify that a string is valid JSON before parsing it or passing it into downstream steps that expect structured data. Typical workflow: receive or construct a JSON string, validate with this node, then conditionally branch—parse or proceed if valid, or log/handle the error if invalid.

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
<tr><td style="word-wrap: break-word;">json_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text to validate as JSON. Supports multiline strings. The node checks only JSON syntax validity.</td><td style="word-wrap: break-word;">{"name": "Salt", "count": 3}</td></tr>
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
<tr><td style="word-wrap: break-word;">is_valid</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True if the input is syntactically valid JSON; otherwise False.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">error_message</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Error message describing why validation failed. Empty string when is_valid is True.</td><td style="word-wrap: break-word;">Expecting property name enclosed in double quotes: line 1 column 2 (char 1)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Validation scope**: This node validates JSON syntax only; it does not validate against a schema or enforce specific fields.
- **Empty success message**: When the JSON is valid, the error_message output is an empty string.
- **Input type**: The input must be a STRING; other types should be converted to string JSON first.
- **Multiline support**: Multiline JSON strings are accepted.
- **Common pitfalls**: Single quotes, trailing commas, and unescaped characters will cause validation to fail.

## Troubleshooting
- **Always invalid with 'Expecting value'**: Ensure the input isn't empty or whitespace-only and that it starts with a valid JSON token (e.g., {, [, ")
- **Using single quotes**: Replace single quotes with double quotes for keys and string values to conform to JSON.
- **Trailing commas**: Remove any trailing commas in objects or arrays.
- **Encoding issues**: If you see an 'Unexpected error', ensure the string is properly encoded (e.g., UTF-8) and characters are correctly escaped.
- **Valid but downstream fails**: Being valid JSON doesn't guarantee the structure matches what downstream steps expect (e.g., object vs array). Inspect the parsed structure.
