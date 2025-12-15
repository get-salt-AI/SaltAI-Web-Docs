# JSON: Filter

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Filters JSON data based on a key/value condition. Expects a list (array) of objects and returns only the objects that match the comparison rule. Supports equals, contains, starts_with, and ends_with comparisons.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonfilter.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to select a subset of objects from JSON data by matching a specific field against a target value. Typically placed after nodes that produce or transform JSON arrays, and before nodes that consume the filtered results.

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
<tr><td style="word-wrap: break-word;">json_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">JSON data to filter. Should be a list (array) of objects. If a string is provided, the node will attempt to parse it as JSON.</td><td style="word-wrap: break-word;">[{"name":"Alice"},{"name":"Bob"}]</td></tr>
<tr><td style="word-wrap: break-word;">filter_key</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The object key (field name) to evaluate for each item in the array.</td><td style="word-wrap: break-word;">name</td></tr>
<tr><td style="word-wrap: break-word;">filter_value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to compare against the item's field. For contains/starts_with/ends_with, comparisons are done as strings.</td><td style="word-wrap: break-word;">Al</td></tr>
<tr><td style="word-wrap: break-word;">filter_mode</td><td>True</td><td style="word-wrap: break-word;">["equals", "contains", "starts_with", "ends_with"]</td><td style="word-wrap: break-word;">How to compare the item's field value with filter_value.</td><td style="word-wrap: break-word;">starts_with</td></tr>
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
<tr><td style="word-wrap: break-word;">filtered_data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Filtered JSON data. If input is a list of objects, returns a list containing only matching objects. In non-list cases or on error, returns the original input.</td><td style="word-wrap: break-word;">[{"name":"Alice"}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input parsing**: If json_data is a string, the node attempts to parse it as JSON.
- **List required**: Actual filtering occurs only when json_data is a list (array). Otherwise, the original data is returned unmodified.
- **Empty key handling**: If filter_key is empty, the original data is returned.
- **Comparison behavior**: equals uses direct equality. contains, starts_with, and ends_with compare string forms of the values.
- **Missing keys**: Objects that do not contain the specified filter_key are ignored (not included in the output).
- **Error handling**: On exceptions, the node returns the original json_data.

## Troubleshooting
- **No items returned**: Verify that json_data is a list of objects and that each object contains the specified filter_key.
- **Unexpected unfiltered output**: Ensure json_data is a list. If it is a single object or another type, the node returns it unchanged.
- **Contains/starts/ends not matching numeric/boolean fields**: These modes compare string representations. Confirm the string form matches your expectation.
- **Invalid JSON string input**: If providing a string, ensure it is valid JSON. Invalid strings will cause the node to return the original input.
- **Equals not matching**: Check for exact type/value equality (e.g., number 1 vs string "1"). Use contains/starts_with/ends_with if appropriate.
