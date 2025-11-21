# JSON: Get Value

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Extracts a value from JSON data using a dot-separated key path. Works with dicts, lists, or JSON strings and supports numeric indices for arrays (e.g., items.0). If the path cannot be resolved, it returns a supplied default value.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsongetvalue.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to read a specific field or nested value from JSON data within a workflow. Typical usage includes pulling a property from an API response, selecting a list item by index, or safely retrieving deeply nested values with a fallback default.

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
<tr><td style="word-wrap: break-word;">json_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The JSON source to read from. Accepts a Python dict, list, or a JSON-formatted string.</td><td style="word-wrap: break-word;">{"user": {"name": "Ada", "id": 123}, "items": [{"title": "First"}]}</td></tr>
<tr><td style="word-wrap: break-word;">key_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Dot-separated path to the target value. Use dots to traverse objects and numeric segments to index lists.</td><td style="word-wrap: break-word;">items.0.title</td></tr>
<tr><td style="word-wrap: break-word;">default_value</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to return if the path does not exist, resolves to None, or an error occurs.</td><td style="word-wrap: break-word;">Not found</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value found at the specified key path. If not found or an error occurs, returns the provided default_value.</td><td style="word-wrap: break-word;">First</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Path format**: Use dot-separated keys for objects and numeric segments for lists (e.g., "user.name", "items.0.title").
- **Input parsing**: If json_data is a string, the node attempts to parse it as JSON before traversal.
- **Empty path behavior**: If key_path is empty, the node returns the original json_data unchanged.
- **Missing values**: If a key is missing, an index is invalid, the traversal hits a non-container, or a step resolves to None, the node returns default_value.
- **Error handling**: On unexpected errors (including invalid JSON strings), the node returns default_value.

## Troubleshooting
- **Returned default instead of value**: Verify the key_path spelling and structure matches the JSON. Ensure array indices are numeric and within range.
- **Invalid JSON string**: If json_data is a string, confirm it is valid JSON. Use a JSON validation tool or a dedicated validator node first.
- **Type mismatch along path**: If a segment expects a dict but encounters a list (or vice versa), adjust the path (e.g., add a numeric index for lists).
- **Received entire input back**: This happens when key_path is empty. Provide a non-empty path to extract a specific value.
- **Unexpected None**: If an intermediate value is None, the node returns default_value. Check your source data or provide a suitable fallback.
