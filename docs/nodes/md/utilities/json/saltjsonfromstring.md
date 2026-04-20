# JSON: From String

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node converts a JSON-formatted text string into a structured data object (object, array, number, boolean, etc.) that other Salt nodes can consume. It uses Salt's internal JSON formatter and returns None if the input string is not valid JSON. This makes it a bridge between text-based JSON sources and nodes that expect structured inputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonfromstring.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you receive or construct JSON as plain text and need to work with its contents programmatically in your Salt pipeline. Typical scenarios include parsing HTTP response bodies, webhook payloads, configuration blobs stored as strings, or model outputs that emit JSON-like text. It usually sits downstream of nodes that output STRING data (for example, an HTTP Request node, a File: Read Text node, or a prompt/template node that formats JSON text), and upstream of nodes that operate on structured data, such as `SaltJsonGetValue`, `SaltJsonSetValue`, `SaltJsonMerge`, or generic transformer nodes.

In a common pattern, you might 1) call an external API that returns JSON text, 2) feed the response body into `SaltJsonFromString` to parse it, and 3) pass the resulting object to `SaltJsonGetValue` to extract specific fields. For user- or model-generated JSON, it is often wise to place `SaltJsonValidate` in the pipeline as well, either before or after this node, to explicitly check for validity and branch your logic accordingly. Because the output is a generic WILDCARD type, design downstream steps with the expected JSON structure in mind.

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
<tr><td style="word-wrap: break-word;">json_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A JSON-formatted text string to be parsed into structured data. Must be syntactically valid JSON: keys in double quotes, proper use of commas and brackets, no trailing commas, and only standard JSON types (object, array, string, number, boolean, null). Multiline strings are supported, which is useful for readability or large payloads.</td><td style="word-wrap: break-word;">{   "user_id": 48291,   "name": "Alex Kim",   "preferences": {     "theme": "dark",     "notifications": true   },   "tags": ["beta", "internal"] }</td></tr>
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
<tr><td style="word-wrap: break-word;">data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The structured data parsed from the input JSON string. This can be an object (dict-like structure), array (list), string, number, boolean, null-equivalent, or None if parsing fails. Downstream nodes can use it as regular structured data to extract fields, iterate over arrays, or transform and re-serialize it.</td><td style="word-wrap: break-word;">{   "user_id": 48291,   "name": "Alex Kim",   "preferences": {     "theme": "dark",     "notifications": true   },   "tags": ["beta", "internal"] }  If parsing fails due to invalid JSON, the output will be:  null</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: For typical API responses and configuration blobs, performance impact is minimal; however, very large JSON strings (hundreds of kilobytes or more) may increase latency, so consider trimming or paging large payloads upstream.
- **Limitations**: The node only accepts strictly valid JSON; inputs using single quotes for strings, comments, or trailing commas are not supported and will result in a None output.
- **Behavior**: On any parsing error, the node logs the issue internally and returns None rather than stopping the pipeline, so downstream logic should explicitly handle the possibility of a None value.
- **Behavior**: Because the output type is WILDCARD, its exact structure (object, array, scalar) depends entirely on the input JSON; ensure downstream nodes and field paths align with the actual shape of the parsed data.

## Troubleshooting
- **Common Error 1**: Output is null/None instead of an object or array. Cause: the input string is invalid JSON (for example, missing quotes around keys or having a trailing comma). Fix: validate the JSON with `SaltJsonValidate` or an external linter and correct the syntax before passing it here.
- **Common Error 2**: Downstream node errors due to unexpected data shape (e.g., expecting an object but getting a list). Cause: the root of the JSON is an array or scalar instead of an object. Fix: adjust the upstream JSON structure (e.g., wrap it in an object) or reconfigure downstream nodes to handle the actual root type.
- **Common Error 3**: Cannot access a field with `SaltJsonGetValue` or similar nodes. Cause: the actual JSON nesting differs from what you assumed (for example, data is under `"data"` or `"result"` instead of top-level). Fix: inspect the original `json_string` to confirm the real structure, then update the path or logic of downstream nodes accordingly.
