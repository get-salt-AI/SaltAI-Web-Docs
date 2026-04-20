# JSON: Set Value

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node updates JSON-like data by setting a value at a specified dot-separated path (for example, "user.profile.name" or "items.0.title"). It accepts dicts, lists, or JSON strings and parses strings into JSON automatically. While traversing the path, it creates missing intermediate dicts or list elements so the target location exists when possible.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonsetvalue.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to build or modify JSON structures dynamically in a Salt workflow, such as constructing API request payloads, assembling configuration objects, or inserting model outputs into nested fields. It often follows nodes that produce or parse structured data (for example, "JSON: From String" or HTTP response/database read nodes) and precedes nodes that serialize or send JSON (for example, "JSON: To String", HTTP request, or storage nodes). Combine it with "JSON: Get Value" for read–modify–write flows (read a field, change it, then write it back) and with "JSON: Merge" when composing objects from multiple sources and then tweaking specific paths. Prefer this node instead of string templating when working with nested structures, because it understands dict/list navigation, automatically creates missing levels, and avoids breaking JSON syntax.

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
<tr><td style="word-wrap: break-word;">json_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The JSON data to modify. Can be a Python dict, list, or a JSON string. When a string is provided, the node attempts to parse it as JSON; invalid or empty strings are treated as an empty object. If key_path is empty, this input is ignored and the node will just return the value.</td><td style="word-wrap: break-word;">{"user": {"name": "Alice", "age": 30}, "items": [{"id": 1, "title": "Book"}]}</td></tr>
<tr><td style="word-wrap: break-word;">key_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Dot-separated path indicating where to set the value. Each segment is used as a dict key or, if it is an integer string, as a list index. When navigating lists, segments must be numeric (for example, "0", "1"); using a non-numeric segment on a list causes the node to return the original data unchanged. An empty string means "replace the entire structure" and the node will output the value directly.</td><td style="word-wrap: break-word;">user.profile.display_name</td></tr>
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to assign at the specified path. Can be any JSON-compatible type (string, number, boolean, null, dict, list) or other pipeline data types; it is stored as-is in the resulting structure at the final key or index.</td><td style="word-wrap: break-word;">Alice Cooper</td></tr>
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
<tr><td style="word-wrap: break-word;">updated_data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The modified JSON data after setting the value at the given path. This is the original structure (or a parsed version if the input was a string) with intermediate dicts/lists created and the final key or index set. If key_path is empty, this output is simply equal to value.</td><td style="word-wrap: break-word;">{"user": {"name": "Alice", "age": 30, "profile": {"display_name": "Alice Cooper"}}, "items": [{"id": 1, "title": "Book"}]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Intermediate structure creation**: Missing dict keys along the path are created as empty dicts, and lists are extended with empty dicts (for intermediate segments) or null values (for final indices) until the requested index exists.
- **Empty path behavior**: If key_path is an empty string, the node does not navigate json_data and instead returns the value directly, effectively replacing the entire JSON object or array.
- **Invalid navigation handling**: If a path segment attempts to index a list with a non-integer, or traversal encounters a non-dict/non-list value at an intermediate step, the node stops and returns the original json_data unchanged.
- **String parsing semantics**: When json_data is a string, it is parsed as JSON; on parse failure or empty/whitespace-only input, the node falls back to an empty object and builds a fresh structure along the specified path.
- **Pipeline usage**: Only the updated_data output reflects the modified structure; other branches using earlier references to json_data will not see this change unless rewired to use updated_data.

## Troubleshooting
- **No change in output**: Symptom: updated_data is identical to json_data. Causes: a non-numeric path segment was used while navigating a list (for example, "items.first.title"), or a path segment hit a primitive value instead of a dict/list. Fix: Use numeric indices for lists (for example, "items.0.title") and ensure upstream nodes yield dicts/lists at each level in the path.
- **Output is just the new value**: Symptom: The node returns only the assigned value, discarding the original structure. Cause: key_path was left empty. Fix: Set a specific path like "user.name" when you intend to update a field within the object.
- **Unexpected empty objects or nulls in arrays**: Symptom: Extra {} objects or null entries appear in lists. Cause: The node extends lists to reach high indices (for example, setting "items.3.title" when items previously had fewer elements). Fix: Check that your indices are correct and avoid skipping large index ranges unintentionally.
- **Original JSON content missing after string input**: Symptom: When passing JSON as a string, the output only shows fields created by this node. Cause: The string could not be parsed as valid JSON and was treated as an empty object. Fix: Validate the JSON with "JSON: Validate" or parse it first with "JSON: From String" before using this node.
