# JSON: Filter

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Filters JSON data based on a key-value criterion. It expects an array of objects and returns only the objects that satisfy the selected comparison mode. If the input is not an array of objects or the key is missing, it returns the original data unchanged.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonfilter.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to extract a subset of items from a JSON array based on the value of a specific key. Typical workflows include filtering API responses, narrowing down lists of records, or post-processing generated JSON. Connect any JSON-compatible output (object, array, or JSON string). If the input is a JSON string, it will be parsed automatically.

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
<tr><td style="word-wrap: break-word;">json_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The JSON content to filter. Preferably an array of objects (e.g., a list of dictionaries). If a string is provided, it will be parsed as JSON. Non-array inputs pass through unchanged.</td><td style="word-wrap: break-word;">[{"status":"open","id":1},{"status":"closed","id":2}]</td></tr>
<tr><td style="word-wrap: break-word;">filter_key</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The object key to evaluate for filtering. Only items containing this key are considered for filtering.</td><td style="word-wrap: break-word;">status</td></tr>
<tr><td style="word-wrap: break-word;">filter_value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to compare against the item's value at filter_key. Type is flexible; comparisons depend on the selected filter_mode.</td><td style="word-wrap: break-word;">open</td></tr>
<tr><td style="word-wrap: break-word;">filter_mode</td><td>True</td><td style="word-wrap: break-word;">["equals", "contains", "starts_with", "ends_with"]</td><td style="word-wrap: break-word;">How to compare the item's value to filter_value. 'equals' uses direct equality; the others perform string-based comparisons.</td><td style="word-wrap: break-word;">equals</td></tr>
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
<tr><td style="word-wrap: break-word;">filtered_data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The filtered JSON result. If input was an array of objects, this is an array containing only the matching objects. If input was not an array or filtering was not applicable, this is the original input.</td><td style="word-wrap: break-word;">[{"status":"open","id":1}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- If json_data is a string, it is parsed as JSON. Invalid JSON strings will cause the node to return the original input.
- Filtering is only applied when json_data is an array (list) of objects; otherwise, the node returns the input unchanged.
- Only items that are dictionaries and contain the specified filter_key are considered; other items are ignored.
- For 'contains', 'starts_with', and 'ends_with', both the item value and filter_value are compared as strings.
- If no items match, the output is an empty array.
- An empty filter_key results in no filtering (the original data is returned).
- Nested keys are not supported; filter_key must be a top-level key in each object.
- Comparisons are case-sensitive. There is no regex support.

## Troubleshooting
- No results returned: Verify that json_data is an array of objects and that each relevant object contains the specified filter_key.
- Filtering has no effect: Ensure filter_key is not empty and that filter_mode is appropriate for the data type.
- Unexpected matching behavior: Remember that non-'equals' modes coerce values to strings; check for case sensitivity and whitespace.
- Error parsing JSON string: Provide valid JSON in json_data or pass a parsed structure (array/object) instead.
- Numeric comparisons: Use 'equals' for numeric equality. Other modes convert values to strings, which may produce unexpected matches.
