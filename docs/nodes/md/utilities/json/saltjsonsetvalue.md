# JSON: Set Value

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Sets a value inside JSON data using a dot-separated key path. It supports nested objects and arrays, auto-creating intermediate dictionaries and extending arrays as needed. Accepts either JSON objects/lists or JSON strings and returns the updated JSON data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonsetvalue.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to programmatically add or update fields in a JSON structure before passing it to subsequent steps (e.g., building request bodies, shaping configuration, or enriching data). Provide the JSON input, a dot-separated path (keys for objects and numeric indices for arrays), and the value to set.

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
<tr><td style="word-wrap: break-word;">json_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The JSON data to modify. Can be a dict/list object or a JSON-formatted string.</td><td style="word-wrap: break-word;">{"user": {"name": "Ann"}}</td></tr>
<tr><td style="word-wrap: break-word;">key_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Dot-separated path indicating where to set the value. Use object keys for maps and numeric indices for arrays.</td><td style="word-wrap: break-word;">user.profile.age or items.0.title</td></tr>
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to set at the specified path. Can be any JSON-serializable type.</td><td style="word-wrap: break-word;">42</td></tr>
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
<tr><td style="word-wrap: break-word;">updated_data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The JSON data after setting the specified value at the given path. If input was a JSON string, the output is a parsed JSON object/list.</td><td style="word-wrap: break-word;">{"user": {"profile": {"age": 42}}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Key path format**: Use dot notation for nested keys. Use numeric segments to index lists (e.g., "items.2.name").
- **Auto-creation**: Missing intermediate objects are created as empty dictionaries. When navigating lists, they are extended with empty objects during traversal and with nulls to reach the final index.
- **String input handling**: If json_data is a string, it is parsed before modification. Invalid JSON will result in returning the original data on error.
- **Empty key_path behavior**: If key_path is empty, the node returns the provided value directly as output.
- **Invalid list index segments**: Non-integer segments for list navigation or final index cause the node to return the original json_data unchanged.
- **Type constraints**: If the path navigates through a value that is neither a dict nor a list, the operation is aborted and the original json_data is returned.

## Troubleshooting
- **Value not updated**: Verify key_path uses correct dot-notated keys and integer indices for arrays (e.g., "items.0.title"). Non-integer indices into lists will cause no change.
- **Output type changed unexpectedly**: If input was a JSON string, the output is a parsed object/list. Convert back to string later if a string is required.
- **Got original JSON back**: This happens when the path traverses a non-dict/list value or an invalid list index segment is used. Adjust the path or initialize the structure earlier.
- **Empty key_path returned the value**: This is by design. Provide a non-empty key_path to update within the JSON structure.
- **Array index out of range**: The node automatically extends arrays as needed. If you still see issues, ensure the parent path segments resolve to a list, not a dict or scalar.
- **Invalid JSON string input**: Ensure json_data is valid JSON when passed as a string. If invalid, parsing fails and the node returns the original data.
