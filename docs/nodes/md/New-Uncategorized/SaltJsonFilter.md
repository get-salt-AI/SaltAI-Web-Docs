# JSON: Filter

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Filters JSON data based on a key-value condition. Accepts a list (array) of objects and returns only those objects whose specified key matches the given value according to the selected mode (equals, contains, starts_with, ends_with). If the input is a JSON string, it will be parsed before filtering.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/SaltJsonFilter.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to narrow down an array of JSON objects to only those matching a simple key-value rule. Typical workflow: load or produce JSON data (often an array of objects), then filter it by a specific field and comparison mode before passing the result to subsequent processing nodes.

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
<tr><td style="word-wrap: break-word;">json_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The JSON data to filter. Should be an array (list) of objects. If provided as a string, it will be parsed.</td><td style="word-wrap: break-word;">[{"status":"active","id":1},{"status":"inactive","id":2}]</td></tr>
<tr><td style="word-wrap: break-word;">filter_key</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The object key to evaluate for each item in the array.</td><td style="word-wrap: break-word;">status</td></tr>
<tr><td style="word-wrap: break-word;">filter_value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to compare against for the chosen key. Used differently depending on filter_mode.</td><td style="word-wrap: break-word;">active</td></tr>
<tr><td style="word-wrap: break-word;">filter_mode</td><td>True</td><td style="word-wrap: break-word;">ENUM: equals\|contains\|starts_with\|ends_with</td><td style="word-wrap: break-word;">How to compare the item's value for filter_key with filter_value. equals uses exact equality; contains, starts_with, and ends_with perform string-based checks.</td><td style="word-wrap: break-word;">equals</td></tr>
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
<tr><td style="word-wrap: break-word;">filtered_data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The filtered JSON data. Typically a list of objects matching the filter condition. If filtering is not applicable, the original data is returned.</td><td style="word-wrap: break-word;">[{"status":"active","id":1}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input type**: The node expects json_data to be a list (array) of objects. If it's not a list, the data is returned unchanged.
- **String parsing**: If json_data is a string, it will be parsed as JSON before filtering.
- **Key requirement**: If filter_key is empty, no filtering is done and the original data is returned.
- **Missing keys**: Objects without the specified filter_key are ignored (not included in the result).
- **Comparison behavior**: equals uses exact equality without converting types; contains, starts_with, and ends_with compare using string representations of values.
- **Error handling**: On parsing or runtime errors, the node logs the error and returns the original input unmodified.

## Troubleshooting
- **No filtering occurs**: Ensure json_data is an array of objects and filter_key is not empty.
- **Unexpected matches with 'equals'**: Check for type mismatches (e.g., number vs string). equals does not coerce types.
- **Contains/starts/ends not matching**: These modes are string-based and case-sensitive. Convert values to a consistent case beforehand if needed.
- **Got original data back**: This happens if input wasn't a list, filter_key was empty, or an internal error occurred. Validate your input format.
- **JSON string input fails**: Verify the JSON string is valid and represents an array of objects.
