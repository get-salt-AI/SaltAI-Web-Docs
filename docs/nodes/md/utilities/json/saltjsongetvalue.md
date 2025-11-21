# JSON: Get Value

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Extracts a value from JSON data using a dot-separated key path. Supports dictionaries, lists, and JSON strings, automatically parsing strings when needed. Returns a default value when the path is missing, invalid, or leads to a null segment.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsongetvalue.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to pull a specific field from structured JSON for downstream processing. Typical workflow: provide an API response or constructed JSON, specify a key path like user.name or items.0.title, and route the extracted value to subsequent nodes.

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
<tr><td style="word-wrap: break-word;">json_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">JSON input to read from. Can be a dict, list, or a JSON-formatted string that will be parsed.</td><td style="word-wrap: break-word;">{"user": {"name": "Alex"}, "items": [{"title": "Book"}]}</td></tr>
<tr><td style="word-wrap: break-word;">key_path</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Dot-separated path to the target value. Use numeric segments to index lists.</td><td style="word-wrap: break-word;">items.0.title</td></tr>
<tr><td style="word-wrap: break-word;">default_value</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Value to return if the path does not exist, is invalid, or resolves to a null segment.</td><td style="word-wrap: break-word;">N/A</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value found at the specified key path, or the provided default when not found.</td><td style="word-wrap: break-word;">Book</td></tr>
</tbody>
</table>
</div>

## Important Notes
- If json_data is a string, it will be parsed as JSON before path resolution.
- An empty key_path returns the entire json_data as-is.
- Use numeric segments (e.g., 0, 1, 2) to access list indices within the path.
- If any path step is missing, invalid, or evaluates to null, the node returns default_value (or None if not provided).
- Works with nested structures such as user.profile.name or config.servers.2.host.

## Troubleshooting
- Path not returning a value: Verify the key_path matches the structure and casing of your JSON keys.
- Index errors on lists: Ensure numeric segments match valid indices (e.g., items.0 for the first element).
- String input not parsed: Confirm the json_data string is valid JSON; otherwise provide a parsed object.
- Unexpected None result: Provide default_value to avoid None when a segment is missing or null in the data.
- Complex paths with numbers in keys: If object keys include dots or numeric-like names, consider restructuring data or extracting in steps to avoid ambiguity.
