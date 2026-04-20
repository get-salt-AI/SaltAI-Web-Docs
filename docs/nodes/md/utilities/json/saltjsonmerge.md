# JSON: Merge

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node merges two JSON objects into a single result, with configurable behavior for how nested data is combined. It accepts both Python dictionaries and JSON-formatted strings and can perform a shallow overwrite merge or a recursive deep merge. In deep mode, nested dictionaries are merged key-by-key, while non-dict values are overwritten by the second object.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonmerge.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to combine two JSON payloads, configuration maps, or partial response fragments into a single JSON object. Typical use cases include merging base configuration with environment-specific overrides, enriching an API response with additional computed fields, or combining user preferences with system defaults. Place this node after any step that produces JSON data, such as SaltJsonFromString (for parsing raw JSON text), upstream utility nodes like SaltJsonSetValue, or API/integration nodes that output JSON. Its result is commonly fed into further JSON manipulation nodes (SaltJsonGetValue, SaltJsonFilter, SaltJsonValidate) or serialization nodes like SaltJsonToString before sending data to external services. Choose "shallow" merge when you primarily want json2 to override json1 at the top level without affecting the internal structure of nested objects. Use "deep" (default) when you need nested dictionaries to be merged recursively so that overlapping keys at each level are updated but existing nested data is preserved. When you need to merge more than two objects, chain multiple JSON: Merge nodes in sequence or construct one side upstream before merging.

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
<tr><td style="word-wrap: break-word;">json1</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Base JSON object for the merge. Can be a Python dict or a JSON-formatted string. After optional parsing, it must represent a JSON object (key–value map), not an array or primitive, for a true merge to occur. Keys here may be preserved or overwritten depending on json2 and the merge mode.</td><td style="word-wrap: break-word;">{"app": {"theme": "light", "language": "en"}, "features": {"beta": false}, "timeout": 30}</td></tr>
<tr><td style="word-wrap: break-word;">json2</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Overlay JSON object applied on top of json1. Can be a Python dict or a JSON-formatted string and should represent a JSON object for merging. In shallow mode, keys in this object overwrite the same keys in json1. In deep mode, its nested dicts are merged recursively into those in json1, while non-dict values overwrite.</td><td style="word-wrap: break-word;">{"app": {"theme": "dark"}, "features": {"beta": true, "logging": true}, "retry": 3}</td></tr>
<tr><td style="word-wrap: break-word;">merge_mode</td><td>True</td><td style="word-wrap: break-word;">str</td><td style="word-wrap: break-word;">Specifies how to merge json1 and json2. "shallow" performs a one-level merge: for each top-level key, the value from json2 replaces the value from json1. "deep" (default) performs a recursive merge for dict-vs-dict values: nested dictionaries are merged key-by-key, while non-dict values from json2 overwrite those in json1.</td><td style="word-wrap: break-word;">deep</td></tr>
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
<tr><td style="word-wrap: break-word;">merged_data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The merged JSON result, typically a Python dict representing the combination of json1 and json2. If both inputs are valid JSON objects, this will contain all keys from both, with conflicts resolved according to the chosen merge mode. If either input is not a dict after parsing, the node returns json2 instead. Downstream nodes can use this as standard JSON data for extraction, filtering, validation, or conversion to a string.</td><td style="word-wrap: break-word;">{"app": {"theme": "dark", "language": "en"}, "features": {"beta": true, "logging": true}, "timeout": 30, "retry": 3}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Deep mode walks nested dictionaries recursively; merging very large or deeply nested JSON structures can be slower. Prefer shallow merges when you only need top-level overrides.
- **Limitations**: True merging only occurs when both inputs (after any string parsing) are dict-like JSON objects. If either is not a dict (for example, a list, string, or number), the node will skip merging logic and simply return json2.
- **Behavior**: When json1 or json2 is a string, the node attempts to parse it as JSON. If parsing fails and raises an error, the node logs the error and returns json1 unchanged as the output.
- **Behavior**: In deep mode, only dict-vs-dict values are merged recursively. For keys where either side is a non-dict (such as lists, strings, or numbers), the value from json2 fully replaces the value from json1 at that key.

## Troubleshooting
- **Output is identical to json2 instead of a merged object**: This usually means one or both inputs are not dictionaries after parsing (for example, a JSON array or primitive). Ensure both json1 and json2 are JSON objects with key–value pairs before merging.
- **Error merging JSON (logged) and output equals json1**: An exception occurred, commonly due to invalid JSON strings that cannot be parsed. Validate the JSON syntax in string inputs or use SaltJsonValidate upstream to check and correct malformed JSON before merging.
- **Nested fields are not combining, only overwritten**: This indicates that merge_mode is set to "shallow". Switch merge_mode to "deep" if you want nested dictionaries to be merged rather than replaced as whole subtrees.
- **Unexpected replacement of nested structures**: In deep mode, if a key maps to a dict in json1 but to a non-dict (e.g., list or string) in json2, or vice versa, the value from json2 replaces the entire structure in json1. Inspect and align the expected data types for conflicting keys in both inputs.
