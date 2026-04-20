# JSON: Validate

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node validates that an input string is syntactically correct JSON. It attempts to parse the string and returns a boolean flag indicating validity along with an error message when parsing fails. It is useful as a guard step before downstream nodes that assume well-formed JSON.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonvalidate.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you accept or construct JSON as plain text and need to ensure it is valid before further processing. Typical scenarios include validating user-provided JSON from forms or prompts, checking JSON returned by external APIs before parsing, or verifying that an LLM-produced JSON structure is syntactically correct. It usually sits early in a JSON-processing pipeline, often following nodes that generate or transform strings (for example, an LLM output node) and just before nodes like `SaltJsonFromString`, `SaltJsonGetValue`, or `SaltJsonMerge` that expect well-formed JSON. A common pattern is: (1) generate or receive JSON as text, (2) pass it into JSON: Validate, (3) branch on the `is_valid` output—if true, pass the original string into JSON: From String to decode it; if false, log or display the error_message and either stop or repair the JSON. Best practice is to always validate JSON from untrusted sources and to surface the error_message in logs or debug UIs to quickly identify structural issues.

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
<tr><td style="word-wrap: break-word;">json_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A text string that is expected to contain JSON. It must be a complete JSON value: typically an object (for example, {"key": "value"}) or array (for example, [1, 2, 3]), but any valid JSON scalar (string, number, boolean, null) is accepted. Leading or trailing whitespace is allowed, but partial or malformed content (extra commas, trailing characters, unquoted keys) will be reported as invalid.</td><td style="word-wrap: break-word;">{   "user": {     "id": 42,     "name": "Ada Lovelace",     "roles": ["admin", "editor"]   },   "active": true }</td></tr>
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
<tr><td style="word-wrap: break-word;">is_valid</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True if the input string was successfully parsed as JSON, otherwise False. Downstream nodes can use this flag to branch logic, for example, only attempting parsing or key extraction when this is true.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">error_message</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">An empty string when the JSON is valid. When invalid, contains a descriptive error message from the JSON parser, including details such as the character index and nature of the problem. This is intended for debugging, logging, or user feedback, not for further machine parsing.</td><td style="word-wrap: break-word;">Expecting ',' delimiter: line 4 column 5 (char 58)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Validation runs a full JSON parse under the hood; for extremely large JSON strings (multi-megabyte), this can be moderately expensive, so avoid validating the same payload repeatedly in a tight loop.
- **Limitations**: This node only checks JSON syntax, not schema or business rules—well-formed but semantically wrong data (for example, missing required fields) will still be marked as valid.
- **Behavior**: On success the error_message is always an empty string; on failure it contains either the raw parser error or a generic "Unexpected error" message, which can vary slightly between environments.
- **Behavior**: Any non-string-like input must be converted to STRING upstream; this node does not auto-serialize objects or other types before validation.

## Troubleshooting
- **Common Error 1**: is_valid=false and error_message like "Expecting property name enclosed in double quotes". This usually means the JSON uses single quotes or unquoted keys. Ensure keys and string values use double quotes and that the structure matches strict JSON syntax.
- **Common Error 2**: is_valid=false with error_message mentioning "Extra data". The input string likely contains more than one JSON value or trailing text (for example, a JSON object followed by another token). Trim or split the input so only a single JSON value is passed.
- **Common Error 3**: is_valid=false and error_message starts with "Unexpected error". This indicates a non-standard failure such as passing a non-string type or corrupted encoding. Confirm that the upstream node outputs a plain UTF-8 STRING and that no binary or partially downloaded content is being passed in.
