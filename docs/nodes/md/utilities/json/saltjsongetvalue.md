# JSON: Get Value

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node reads JSON-like data (dict, list, or JSON string) and returns the value found at a specified dot-separated key path. It supports both object keys and list indices such as items.0.title and falls back to a user-provided default when the path is missing or invalid. It automatically parses JSON strings before traversal, making it flexible for different upstream data sources.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsongetvalue.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to pull out a specific field from structured JSON data in a Salt workflow. Typical use cases include extracting a user name from an API response, reading a particular property from tool output, or selecting a single item from a list of results. It usually sits downstream of nodes that produce JSON or JSON-like structures, such as JSON: From String, API or integration nodes that return dicts or lists, or other JSON: Merge or JSON: Set Value nodes.

Place JSON: Get Value after a node that outputs an object, list, or JSON text. Configure key_path using dot notation, where segments can be object keys or zero-based list indices, for example user.profile.email, messages.0.content, or config.features.beta.enabled. Connect its single output to downstream consumers that expect just that scalar or sub-structure, such as prompt construction nodes, routing or branching logic nodes, or condition evaluators. Combine it with JSON: Validate and JSON: From String for robust handling of untrusted JSON, and with JSON: Set Value or JSON: Merge when you need to modify and then re-read parts of a JSON document.

Define a sensible default_value when the target path may be absent or data may be incomplete, such as optional fields in API responses. This prevents runtime failures and gives downstream nodes predictable input. Keep key paths simple and explicit; avoid relying on ambiguous types in intermediate steps (for example, ensure items is always a list before using numeric indices).

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
<tr><td style="word-wrap: break-word;">json_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Source JSON data to read from. Can be a dict, list, or a JSON-formatted string. If it is a string, the node will attempt to parse it as JSON first. The structure should match the key path you intend to navigate, using objects for named keys and lists for numeric indices. Invalid JSON strings will cause parsing errors and the node will fall back to the default value.</td><td style="word-wrap: break-word;">{ "user": { "name": "Dana", "roles": ["admin", "editor"] }, "settings": { "theme": "dark" } }</td></tr>
<tr><td style="word-wrap: break-word;">key_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Dot-separated path describing where to find the desired value inside the JSON structure. Each segment is either an object key for dicts or a zero-based index for lists. Examples: user.name, user.roles.0, items.0.title, config.database.host. If left empty, the entire json_data is returned unchanged.</td><td style="word-wrap: break-word;">user.roles.0</td></tr>
<tr><td style="word-wrap: break-word;">default_value</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fallback value returned when the key path cannot be resolved, including missing keys, out-of-range indices, type mismatches on the path, null encounters, or parsing errors. If not provided, the node returns None in those cases. Choose a type that downstream nodes can safely handle, such as an empty string, empty dict or list, or a clear sentinel value.</td><td style="word-wrap: break-word;">unknown</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value found at the specified key path within the JSON data, after optional JSON-string parsing. This may be any JSON-compatible type such as string, number, boolean, null, object, array, or a nested structure. If the path is invalid or any traversal step fails, this output is the provided default_value, or None if not supplied. Downstream nodes can treat this as a direct scalar or as sub-JSON to further process or embed.</td><td style="word-wrap: break-word;">"admin"</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Traversal is lightweight, but repeatedly parsing large JSON strings can be expensive; when possible, parse once with JSON: From String and pass structured data instead of raw text.
- **Limitations**: If any intermediate lookup results in a null or None value, the node immediately returns the default value instead of continuing deeper into the path, which means you cannot distinguish between an explicit null in JSON at an intermediate step and a missing path.
- **Behavior**: When json_data is a string, the node tries to parse it as JSON; if parsing fails for any reason, it logs an error internally and returns the default value without raising a user-visible exception.
- **Behavior**: For list navigation, each path segment must be a valid integer index; non-integer segments on a list, such as items.first, or out-of-range indices cause an early fallback to the default value.
- **Limitations**: The node does not support advanced query features like wildcards, filters, or JSONPath; only straightforward dot-separated key names and numeric indices are allowed.

## Troubleshooting
- **Path returns default unexpectedly**: If you always get the default value instead of the expected field, verify that the key_path exactly matches the structure of json_data, including correct key names, nesting, and zero-based list indices. Also confirm that intermediate keys do not contain dots in their actual names, as these cannot be represented.
- **Type mismatch on path traversal**: When a segment expects a dict but the actual value is a list, or vice versa, the node will fall back to default_value. Inspect the upstream JSON with a helper node like JSON: To String or any log or preview functionality to confirm the actual structure before choosing a key path.
- **Invalid JSON string input**: If json_data is a malformed JSON string, the node cannot parse it and will return the default value. Run the same string through JSON: Validate and JSON: From String first to catch and fix formatting issues.
- **Index errors in arrays**: If you see defaults when targeting list elements, the list may be shorter than assumed. For example, items.2 will fail when there are fewer than three items. Check array lengths upstream or guard by using a safe default_value like an empty dict or list.
