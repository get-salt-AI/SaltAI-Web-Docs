# JSON: Merge

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Merges two JSON objects into a single object. Supports shallow merge (json2 overwrites json1 at the top level) and deep merge (recursively merges nested dictionaries). Accepts Python dicts or JSON strings; strings are safely parsed before merging.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonmerge.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to combine two JSON/dictionary structures, such as overlaying environment-specific configuration over defaults or merging partial payloads. Choose 'shallow' to simply overwrite top-level keys with json2, or 'deep' to recursively merge nested dictionaries while still letting json2 take precedence where conflicts occur.

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
<tr><td style="word-wrap: break-word;">json1</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The base JSON object. Can be a dict or a JSON-formatted string.</td><td style="word-wrap: break-word;">{'defaults': {'retries': 3, 'timeout': 30}, 'features': {'beta': False}}</td></tr>
<tr><td style="word-wrap: break-word;">json2</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The overlay JSON object whose values take precedence. Can be a dict or a JSON-formatted string.</td><td style="word-wrap: break-word;">{'defaults': {'timeout': 60}, 'features': {'beta': True}}</td></tr>
<tr><td style="word-wrap: break-word;">merge_mode</td><td>True</td><td style="word-wrap: break-word;">shallow \| deep</td><td style="word-wrap: break-word;">Select 'shallow' to overwrite only at the top level, or 'deep' to recursively merge nested dictionaries. In both modes, json2 values win on conflict.</td><td style="word-wrap: break-word;">deep</td></tr>
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
<tr><td style="word-wrap: break-word;">merged_data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The merged JSON object. Typically a dict; if inputs are not dicts, the output falls back to json2.</td><td style="word-wrap: break-word;">{'defaults': {'retries': 3, 'timeout': 60}, 'features': {'beta': True}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Deep merge only recurses into dictionary values; arrays/lists and non-dict values from json2 will overwrite json1 at that key.
- If an input is a JSON string, the node will attempt to parse it. Invalid or empty JSON strings are treated as empty objects when merging.
- If either json1 or json2 is not a dictionary after parsing, the node returns json2 as-is.
- Shallow merge behavior is equivalent to overwriting json1 keys with json2 keys at the top level.
- On unexpected errors, the node returns json1 unchanged.

## Troubleshooting
- Merged result is not a dict: Ensure both inputs are dictionaries or valid JSON strings that parse to objects; lists or primitives cause the node to return json2.
- Deep merge didn't merge arrays: This node does not merge arrays/lists; json2's list replaces json1's list at the same key.
- Invalid JSON string input: Validate or correct the JSON string; invalid strings are treated as empty objects, leading to fewer keys than expected.
- Unexpected unchanged output: In case of internal errors the node returns json1; check logs and verify inputs are valid dictionaries or JSON strings.
- Keys missing after shallow merge: Remember shallow mode overwrites only at the top level—nested dictionaries are not recursively combined in 'shallow' mode.
