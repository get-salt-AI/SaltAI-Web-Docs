# JSON: Set Value

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Sets a value inside JSON-like data using a dot-separated key path. Accepts dictionaries, lists, or JSON strings, and will create intermediate objects/arrays as needed. If the path targets a list index, the list is expanded as necessary.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonsetvalue.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to insert or update a value at a specific nested location within JSON data. It is commonly used to build up configuration objects step-by-step, modify API payloads, or update items within arrays using numeric indices in the key path.

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
<tr><td style="word-wrap: break-word;">json_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The input data to modify. Can be a dict, list, or a JSON-formatted string. If a JSON string is provided and cannot be parsed, an empty object is used as the starting point.</td><td style="word-wrap: break-word;">{"user": {"name": "Alex"}}</td></tr>
<tr><td style="word-wrap: break-word;">key_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Dot-separated path indicating where to set the value. Use object keys for dictionaries and numeric segments for list indices (e.g., items.0.title).</td><td style="word-wrap: break-word;">user.profile.age</td></tr>
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to set at the specified path. Can be any JSON-serializable type or structured data.</td><td style="word-wrap: break-word;">30</td></tr>
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
<tr><td style="word-wrap: break-word;">updated_data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The modified JSON data with the value set at the provided path.</td><td style="word-wrap: break-word;">{"user": {"name": "Alex", "profile": {"age": 30}}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Empty path behavior**: If key_path is empty, the output becomes the provided value (replaces the entire input).
- **String inputs**: If json_data is a string, the node attempts to parse it. If parsing fails or yields null/empty, it starts from an empty object.
- **Intermediate creation**: Missing intermediate keys become objects (dicts). For list navigation, numeric segments are required; lists are extended with placeholders as needed.
- **List indices**: Path segments targeting lists must be valid integers. Non-integer segments for lists cause the operation to be skipped and the original data returned.
- **Type constraints**: If a non-dict/non-list is encountered before reaching the final key, the operation is skipped and the original data is returned.

## Troubleshooting
- **Value not updated**: Ensure that any segment referring to a list is a valid integer (e.g., 'items.0.name', not 'items.first.name').
- **Output unchanged**: A non-dict/non-list encountered along the path prevents updates. Verify earlier segments resolve to dicts or lists.
- **Unexpected empty object**: When providing a JSON string, if parsing fails, the node starts from an empty object. Verify the input string is valid JSON.
- **Overwriting whole object**: An empty key_path replaces the entire object with value. Provide a non-empty path to update a nested field instead.
