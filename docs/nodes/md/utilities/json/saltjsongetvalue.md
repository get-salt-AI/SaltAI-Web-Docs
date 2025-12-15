# JSON: Get Value

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Extracts a value from JSON data using a dot-separated key path. Supports nested objects and arrays (by numeric index). If the path cannot be resolved, the node returns the provided default value or null if none is provided.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsongetvalue.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to pick a specific field from structured JSON data within a workflow. Provide the JSON (as an object, list, or JSON string) and a dot-separated path such as user.name or items.0.title to retrieve the desired value for branching logic, prompts, or downstream processing.

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
<tr><td style="word-wrap: break-word;">json_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">JSON data to read from. Can be a dictionary, list, or a JSON-formatted string.</td><td style="word-wrap: break-word;">{"user": {"name": "Ada", "age": 37}, "items": [{"title": "First"}]}</td></tr>
<tr><td style="word-wrap: break-word;">key_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Dot-separated path to the target value. Use dot notation for nested keys and numeric segments for array indices.</td><td style="word-wrap: break-word;">items.0.title</td></tr>
<tr><td style="word-wrap: break-word;">default_value</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to return if the path is not found or cannot be resolved.</td><td style="word-wrap: break-word;">Unknown</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value found at the specified path. Returns the default value (or null if none provided) when the path is invalid or not found.</td><td style="word-wrap: break-word;">First</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Path format**: Use dot-separated keys; use numeric segments to index arrays (e.g., "items.0.name").
- **JSON input types**: Accepts dicts, lists, or JSON strings. JSON strings are parsed before accessing.
- **Empty path behavior**: If key_path is empty, the entire json_data is returned.
- **Missing path handling**: If any segment is missing, out-of-range, or type-mismatched, the node returns default_value (or null if not provided).
- **None values**: If a resolved segment is None, the node returns default_value.
- **Invalid JSON strings**: If parsing json_data as a string fails, the node returns default_value.

## Troubleshooting
- **Got null instead of a value**: Provide a default_value or verify the key_path matches your data structure exactly.
- **Array index errors**: Ensure array positions exist and use numeric indices in the path (e.g., "items.2").
- **No output change**: If key_path is empty, the node returns the original input. Provide a valid path to extract a specific field.
- **JSON string not parsed**: Confirm json_data is a valid JSON string. Invalid JSON returns the default_value.
