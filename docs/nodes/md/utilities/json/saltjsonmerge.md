# JSON: Merge

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Merges two JSON objects into a single object. Supports shallow merge (json2 overwrites json1 at the top level) and deep merge (nested objects are combined recursively while non-object values are overwritten by json2). If inputs are provided as JSON strings, they are parsed automatically.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonmerge.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to combine configuration objects, augment default data with overrides, or consolidate two JSON structures. Choose 'shallow' to overwrite only top-level keys, or 'deep' to recursively merge nested dictionaries. Place it after nodes that produce JSON data or JSON strings, and before any node that consumes the merged result.

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
<tr><td style="word-wrap: break-word;">json1</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First JSON object (or JSON string) to merge. Typically the base or default data.</td><td style="word-wrap: break-word;">{"api": {"timeout": 10, "retries": 2}, "features": {"beta": false}}</td></tr>
<tr><td style="word-wrap: break-word;">json2</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second JSON object (or JSON string) to merge. Values here overwrite or extend json1 depending on the merge mode.</td><td style="word-wrap: break-word;">{"api": {"timeout": 20}, "features": {"beta": true}}</td></tr>
<tr><td style="word-wrap: break-word;">merge_mode</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">How to merge the two objects. 'shallow' overwrites top-level keys from json2. 'deep' recursively merges nested objects while overwriting non-object values with json2.</td><td style="word-wrap: break-word;">deep</td></tr>
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
<tr><td style="word-wrap: break-word;">merged_data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The merged JSON object. When deep merging, nested dictionaries are combined; otherwise json2 overwrites top-level keys from json1.</td><td style="word-wrap: break-word;">{"api": {"timeout": 20, "retries": 2}, "features": {"beta": true}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input flexibility**: If json1 or json2 are strings, the node attempts to parse them as JSON. Invalid or empty strings parse to an empty object for merging.
- **Type requirement for merging**: Merging applies to dictionaries (objects). If either input is not a dictionary after parsing, the node returns json2 as-is.
- **Deep vs shallow behavior**: Deep merge only combines nested objects (dictionaries). Lists and primitive values are not merged element-wise; json2 values overwrite json1 values for those keys.
- **Default mode**: The default merge_mode is 'deep'. Set to 'shallow' if you only want top-level overwrites.
- **Key conflicts**: On conflicts, json2 takes precedence.

## Troubleshooting
- **Output is unchanged or unexpected**: Ensure both inputs are valid JSON objects (not arrays or primitives). If one input is not an object, the node returns json2.
- **Lists are not combined**: This node does not perform element-wise list merges. Convert lists to objects with meaningful keys or handle list merging upstream.
- **Invalid JSON string input**: If a string fails to parse, it is treated as an empty object. Validate your JSON strings or use a validation node beforehand.
- **Nested data not merging**: Verify merge_mode is set to 'deep'. In 'shallow' mode, only top-level keys are considered.
