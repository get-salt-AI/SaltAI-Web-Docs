# JSON: Set Value

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Sets a value inside JSON data using a dot-separated key path. Supports navigating objects and arrays (using numeric indices for arrays) and creates intermediate objects/arrays as needed. If the key path is empty, the entire data is replaced with the provided value.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltjsonsetvalue.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to insert or update a value within nested JSON data by specifying a path like user.profile.name or items.0.title. It accepts JSON data as a dict/list or a JSON string. Typically used after parsing JSON, or before serializing back to a string, to programmatically modify specific fields.

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
<tr><td style="word-wrap: break-word;">json_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The JSON data to modify. Can be a dict, list, or a JSON string. If a string is provided, it will be parsed.</td><td style="word-wrap: break-word;">{'user': {'name': 'Alice'}, 'items': [{'title': 'Old'}]}</td></tr>
<tr><td style="word-wrap: break-word;">key_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Dot-separated path to where the value should be set. Use numeric segments to index into arrays (e.g., items.0.title). Leave empty to replace the entire input with the provided value.</td><td style="word-wrap: break-word;">items.0.title</td></tr>
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to set at the specified path. Can be any JSON-serializable type.</td><td style="word-wrap: break-word;">New Title</td></tr>
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
<tr><td style="word-wrap: break-word;">updated_data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The JSON data after setting the value at the specified path.</td><td style="word-wrap: break-word;">{'user': {'name': 'Alice'}, 'items': [{'title': 'New Title'}]}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Path format**: Use dot-separated keys; array indices must be numeric (e.g., items.2.name).
- **Empty path**: If key_path is empty, the entire output becomes the provided value.
- **Input as string**: If json_data is a JSON string, it is parsed; if parsing fails or results in null/empty, an empty object {} is used as the base before setting the value.
- **Intermediate creation**: Missing objects/arrays are created automatically along the path. For arrays, missing indices are filled with {} for intermediate steps and None for final padding.
- **Invalid segments**: Non-numeric array indices or encountering non-container types in the path will cause the operation to no-op and return the original data.
- **Return type**: Output is the updated data structure (dict/list/other), not a JSON string.

## Troubleshooting
- **Value not updated**: Ensure array path segments are numeric (e.g., '0', '1') and that intermediate path targets are objects/arrays. Invalid segments cause the node to return the original data.
- **Output became {} or lost fields**: If you passed an invalid JSON string as input, parsing fails and the node starts from an empty object. Validate the JSON first using a JSON validation node or ensure the input string is valid.
- **Setting inside arrays behaves unexpectedly**: When extending arrays to reach an index, intermediate elements are created. For intermediate steps they are {}, and for final padding they are None; verify your path and expected structure.
- **Whole data replaced**: This happens when key_path is empty; provide a proper path if you intend to modify only a part of the data.
