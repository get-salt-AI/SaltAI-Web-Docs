# JSON: Validate

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Checks whether the provided text is valid JSON. Returns a boolean indicating validity and a companion error message string that is empty when valid and explains the parsing issue when invalid.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonvalidate.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to guard workflows that depend on well-formed JSON. Typical patterns include validating API responses before parsing, verifying user-provided configuration, or gating conditional branches that require valid JSON input.

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
<tr><td style="word-wrap: break-word;">json_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text to validate as JSON. Multiline input is supported. Accepts any valid JSON type (object, array, string, number, boolean, null).</td><td style="word-wrap: break-word;">{"name":"Alice","age":30,"skills":["python","sql"]}</td></tr>
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
<tr><td style="word-wrap: break-word;">is_valid</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True if the input is valid JSON; False otherwise.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">error_message</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Empty string when valid; otherwise contains a descriptive parsing error message.</td><td style="word-wrap: break-word;">Expecting property name enclosed in double quotes: line 1 column 2 (char 1)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Syntax-only check**: This node validates JSON syntax; it does not enforce schemas or field requirements.
- **All JSON types allowed**: Objects, arrays, strings, numbers, booleans, and null are valid if properly formatted.
- **Empty or whitespace-only input is invalid**: Such inputs will return is_valid = False with an error message.
- **Error message semantics**: When is_valid is True, error_message is an empty string; when False, it contains the parse error.
- **Input must be a string**: Ensure the value provided is a textual JSON representation, not a pre-parsed structure.

## Troubleshooting
- **Always returns False**: Verify the input uses double quotes for keys/strings, has no trailing commas, and is a string. Remove invisible characters (e.g., BOM) or trim whitespace.
- **Unexpected characters error**: Check for trailing commas, unescaped quotes, or control characters; escape special characters within strings.
- **Encoding issues**: Ensure the input is UTF-8 encoded text if it contains non-ASCII characters.
- **Large JSON inputs**: If validation is slow or memory-intensive, reduce the payload size or validate in parts before full processing.
