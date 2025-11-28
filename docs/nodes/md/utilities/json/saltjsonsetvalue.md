# JSON: Set Value

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Sets a value inside JSON data using a dot-separated key path. Accepts JSON as a Python dict/list or as a JSON string, and will create intermediate objects/arrays as needed along the path. Supports addressing array elements via numeric indices.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonsetvalue.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to add or update a specific field deep inside a JSON structure. Common in workflows where you enrich API responses, prepare payloads, or adjust configuration objects dynamically by path. Provide the target JSON, a dot-separated path (including numeric indices for arrays), and the value to set.

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
<tr><td style="word-wrap: break-word;">json_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The JSON to modify. Can be a dict/list object or a valid JSON string.</td><td style="word-wrap: break-word;">{"user": {"name": "Alex", "prefs": {}}}</td></tr>
<tr><td style="word-wrap: break-word;">key_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Dot-separated path to the field to set. Use numeric segments to target array indices.</td><td style="word-wrap: break-word;">user.prefs.theme</td></tr>
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to set at the specified path. Can be any JSON-serializable type (string, number, boolean, null, object, or array).</td><td style="word-wrap: break-word;">dark</td></tr>
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
<tr><td style="word-wrap: break-word;">updated_data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The JSON data after setting the value at the specified path.</td><td style="word-wrap: break-word;">{"user": {"name": "Alex", "prefs": {"theme": "dark"}}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Paths**: Use dot notation for nested fields (e.g., user.profile.name). Use numeric segments for arrays (e.g., items.0.title).
- **String input**: If json_data is a string, it must be valid JSON. It will be parsed before modification.
- **Empty path**: If key_path is empty, the output becomes the provided value (replaces the entire input).
- **Intermediate creation**: Missing objects/arrays along the path are created automatically. For lists, missing indices are padded.
- **Array indices**: When setting a final array index beyond current length, the list is extended with nulls until the index exists.
- **Type constraints**: If a path segment expects a dict but the current value is not a dict (or expects a list index but is not a list), the operation is not applied and the original json_data is returned.
- **Invalid array index**: Non-numeric array path segments cause the operation to return the original json_data without change.

## Troubleshooting
- **Value not updated**: Verify key_path segments match actual types (dict keys vs numeric indices for arrays). If a non-numeric index is used on a list, the node returns the original data.
- **Got original data back**: The node may have encountered a type mismatch or invalid index. Check each segment of key_path and ensure intermediate structures exist or can be created (objects for keys, arrays for indices).
- **Error parsing input**: If json_data is a string, ensure it is valid JSON. Invalid JSON prevents parsing and modification.
- **Unexpected nulls in arrays**: When setting a far index in a list, intermediate positions are filled with null. Adjust the path or pre-size the array if this is undesirable.
- **Entire data replaced**: If key_path is empty, the node returns the value as the entire output. Provide a non-empty key_path to set a nested field instead.
