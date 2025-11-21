# JSON: Filter

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Filters an array of JSON objects by comparing a specific key's value against a provided value using one of several modes (equals, contains, starts_with, ends_with). Accepts raw JSON data or a JSON string and returns only the objects that match the criteria. If the input isn't a list of objects or the filter key is empty, the original data is returned unchanged.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonfilter.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to subset a list of JSON objects based on a simple key-value rule. Typical workflow: parse or build JSON data, apply filtering with the desired mode, then pass the filtered list into downstream processing (e.g., iteration, transformation, or serialization).

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
<tr><td style="word-wrap: break-word;">json_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">JSON data to filter. Can be a list of objects or a JSON string representing such a list.</td><td style="word-wrap: break-word;">[{"status": "active", "id": 1}, {"status": "inactive", "id": 2}]</td></tr>
<tr><td style="word-wrap: break-word;">filter_key</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The object key to test in each list item. Only top-level keys in each object are considered.</td><td style="word-wrap: break-word;">status</td></tr>
<tr><td style="word-wrap: break-word;">filter_value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to compare against the object's value at filter_key.</td><td style="word-wrap: break-word;">active</td></tr>
<tr><td style="word-wrap: break-word;">filter_mode</td><td>True</td><td style="word-wrap: break-word;">["equals", "contains", "starts_with", "ends_with"]</td><td style="word-wrap: break-word;">Comparison mode to apply. 'equals' uses direct equality; other modes convert both sides to strings for comparison.</td><td style="word-wrap: break-word;">equals</td></tr>
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
<tr><td style="word-wrap: break-word;">filtered_data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The filtered list of JSON objects. If input wasn't a list or filter_key was empty, returns the original input.</td><td style="word-wrap: break-word;">[{"status": "active", "id": 1}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input must be a list of objects**: Filtering occurs only when json_data is a list; otherwise the input is passed through unchanged.
- **Top-level keys only**: The filter_key is checked directly on each object; nested paths are not supported.
- **String coercion in non-equals modes**: For contains/starts_with/ends_with, both values are converted to strings before comparison.
- **Case sensitivity**: All comparisons are case-sensitive.
- **JSON string handling**: If json_data is a string, it will be parsed; invalid JSON parses to None which then passes through unchanged.
- **Non-dict items are ignored**: Items that are not objects (dicts) or that do not contain filter_key won't match.
- **Empty or missing key**: If filter_key is empty, the node returns the original data without filtering.

## Troubleshooting
- **Output is unchanged**: Ensure json_data is a list of objects and filter_key is non-empty; strings must parse into a list.
- **No items matched**: Verify the exact key spelling and value (including case). For partial matches, use 'contains', 'starts_with', or 'ends_with'.
- **Equality not matching numbers/booleans**: 'equals' compares without string coercion; confirm the filter_value type matches the data type in the objects.
- **Unexpected matches with contains/starts_with/ends_with**: These modes cast values to strings; ensure that behavior aligns with expectations.
- **Parsing failed for JSON string**: Validate the input JSON string; invalid JSON will result in None and be returned as-is.
