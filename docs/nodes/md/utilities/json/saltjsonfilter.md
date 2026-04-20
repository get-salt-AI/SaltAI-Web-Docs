# JSON: Filter

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltJsonFilter filters JSON data that represents a list of objects, returning only those items where a specified key matches a given value according to a chosen comparison mode. It supports four modes (equals, contains, starts_with, ends_with) and will parse string input as JSON when possible. If the input is not a list or the filter key is empty, the original data is returned unchanged.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonfilter.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have an array (list) of objects and need to keep only the entries that match a condition on a single field. Typical use cases include filtering API responses such as lists of users or orders, selecting products whose name contains a keyword, or narrowing down logs/events to those starting with a particular prefix. Place it downstream of nodes that build or retrieve JSON-like data, for example after `SaltJsonFromString` (to parse JSON text) or HTTP/integration nodes that return lists. The filtered result is commonly passed to `SaltJsonToString` for serialization, to `SaltJsonGetValue` for extracting specific fields from the filtered items, or into iteration/aggregation nodes.

To use it, provide `json_data` as a list of dictionaries or a JSON string that parses into such a list. Set `filter_key` to the top-level field name you want to inspect in each object, `filter_value` to the value or pattern to match, and `filter_mode` to the desired comparison strategy. Use `equals` for exact comparison (including numeric and boolean fields) and the other modes for string-based pattern matching. For robust pipelines, validate and parse external JSON first with `SaltJsonValidate` and `SaltJsonFromString` so that `json_data` is consistently a list by the time it reaches this node.

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
<tr><td style="word-wrap: break-word;">json_data</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">JSON data to filter. Expected to be a list (array) of objects. If provided as a string, the node attempts to parse it as JSON; if parsing fails or does not produce a list, the data is returned unchanged and no filtering is performed.</td><td style="word-wrap: break-word;">[{"id": 1, "status": "active", "email": "alice@example.com"}, {"id": 2, "status": "inactive", "email": "bob@example.com"}, {"id": 3, "status": "active", "email": "carol@example.org"}]</td></tr>
<tr><td style="word-wrap: break-word;">filter_key</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The top-level field name inside each object to evaluate during filtering. Nested paths like "user.name" are not supported. If left empty, the node will skip filtering and return the input list as-is.</td><td style="word-wrap: break-word;">status</td></tr>
<tr><td style="word-wrap: break-word;">filter_value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value or pattern to compare against the value found at `filter_key` in each object. With `equals`, the node compares values directly (e.g., integer 1 to 1, boolean true to true, or exact string to string). With `contains`, `starts_with`, and `ends_with`, both the field value and this filter_value are converted to strings before comparison.</td><td style="word-wrap: break-word;">active</td></tr>
<tr><td style="word-wrap: break-word;">filter_mode</td><td>True</td><td style="word-wrap: break-word;">["equals", "contains", "starts_with", "ends_with"]</td><td style="word-wrap: break-word;">The comparison mode used to evaluate matches. `equals` keeps objects whose field value is exactly equal to filter_value. `contains` keeps objects where the stringified field value contains the stringified filter_value. `starts_with` and `ends_with` check string prefixes and suffixes, respectively. Comparisons are case-sensitive unless you normalize values upstream.</td><td style="word-wrap: break-word;">equals</td></tr>
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
<tr><td style="word-wrap: break-word;">filtered_data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The filtered JSON data. When `json_data` is a list and `filter_key` is non-empty, this will be a list of objects that passed the filter condition. Elements that are not dictionaries or that lack the specified key are excluded. If the input is not a list, the filter key is empty, or an error occurs, the (possibly parsed) original data is returned unchanged.</td><td style="word-wrap: break-word;">[{"id": 1, "status": "active", "email": "alice@example.com"}, {"id": 3, "status": "active", "email": "carol@example.org"}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Filtering is done in a single pass over the list and is efficient for usual dataset sizes, but extremely large arrays can still add noticeable processing time. If necessary, reduce the dataset earlier or paginate incoming data.
- **Limitations**: Only top-level keys are supported as `filter_key`; nested access (e.g., "user.name") is not interpreted. If you need nested filtering, flatten or pre-process the JSON objects upstream.
- **Behavior**: When `json_data` is a string, the node attempts to parse it as JSON. If parsing fails or if the parsed content is not a list, filtering is skipped, the data is returned as-is, and the error is handled internally rather than throwing an exception.
- **Behavior**: For `contains`, `starts_with`, and `ends_with`, comparisons operate on stringified versions of the field value and filter_value. This may cause unexpected results with numeric or boolean fields, so prefer `equals` when strict non-string matching is required.
- **Limitations**: List elements that are not dictionaries or do not have the specified `filter_key` are silently dropped from the result set. They are neither modified nor treated as errors, they simply do not appear in `filtered_data`.

## Troubleshooting
- **Symptom: No filtering occurs and the output matches the input**: Confirm that `json_data` is actually a list. If it is a single object or another type, the node returns it unchanged. Also check that `filter_key` is not an empty string. You may need to parse a JSON string first with `SaltJsonFromString`.
- **Symptom: Output is an empty list, but items should match**: Verify that `filter_key` is spelled exactly as the field name in your objects (including case) and that the objects contain that key. Ensure that `filter_mode` and `filter_value` are appropriate for your data type—for instance, use `equals` for exact matches and `contains` for substring searches in text.
- **Symptom: Unexpected matches for numeric or boolean fields**: Only `equals` uses direct equality. The other modes convert values to strings, which can cause numbers like 10 to "contain" the digit 1. Switch to `equals` or normalize data to strings upstream with clear formatting.
- **Symptom: Supplying a JSON string results in no filtering**: The string might not be valid JSON or might represent a single object instead of a list. First validate it using `SaltJsonValidate`, then parse it with `SaltJsonFromString`. Ensure the parsed result is a list before feeding it into `SaltJsonFilter`.
