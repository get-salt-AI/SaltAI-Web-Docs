# JSON: Get Value

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Extracts a value from JSON data using a dot-separated key path. Supports nested objects and arrays (use numeric indices for arrays). If the path cannot be resolved, returns the provided default value.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsongetvalue.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to pull a specific field from complex JSON data, such as API responses or configuration blobs. Provide the JSON (object, array, or JSON string) and a dot-separated path like "user.name" or "items.0.title" to retrieve the desired field. Commonly used after parsing a JSON string or before feeding a single value into downstream nodes.

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
<tr><td style="word-wrap: break-word;">json_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The source JSON data. Can be a dictionary, list, or a JSON-formatted string which will be parsed.</td><td style="word-wrap: break-word;">{"user": {"name": "Alice"}, "items": [{"title": "First"}]}</td></tr>
<tr><td style="word-wrap: break-word;">key_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Dot-separated path to the target value. Use dots to navigate objects and integers to index arrays.</td><td style="word-wrap: break-word;">items.0.title</td></tr>
<tr><td style="word-wrap: break-word;">default_value</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to return if the key path is not found or cannot be resolved.</td><td style="word-wrap: break-word;"><fallback></td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value located at the specified key path, or the default value if not found.</td><td style="word-wrap: break-word;">First</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Empty key_path returns input**: If key_path is empty, the entire json_data is returned.
- **String inputs are parsed**: When json_data is a string, the node attempts to parse it as JSON before traversal.
- **Array access**: Use integer segments (e.g., "items.2") to access list elements; non-integer segments on lists will cause a fallback to default_value.
- **Missing keys or None stops traversal**: If any step is missing or resolves to None, the node returns default_value.
- **Invalid JSON strings**: If json_data is a string but cannot be parsed, traversal will fail and default_value is returned.
- **Type-sensitive traversal**: Traversal only works through dict keys and list indices; encountering other types leads to default_value.

## Troubleshooting
- **Got None instead of a value**: Provide default_value to avoid None, and verify the key_path matches the JSON structure.
- **Path not working on arrays**: Ensure array segments in key_path are valid integers (e.g., "0", "1").
- **Index out of range**: Accessing a list element that doesn't exist will return default_value; adjust the index or provide a fallback.
- **Invalid JSON string**: If json_data is a malformed string, it cannot be parsed; validate or correct the input string first.
- **Unexpected type in path**: If a segment targets a list but a dict is present (or vice versa), the node returns default_value; confirm your path matches the actual data shape.
- **Empty or whitespace-only input**: Empty strings parse to None; supply valid JSON or a default_value.
