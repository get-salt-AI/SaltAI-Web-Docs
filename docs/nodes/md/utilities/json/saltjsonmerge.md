# JSON: Merge

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Merges two JSON objects. Supports shallow (overwrite) or deep (recursive) merge modes. If inputs are strings, they are parsed as JSON; in deep mode, nested dictionaries are merged while non-dict values are overwritten by the second input.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/json/saltjsonmerge.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to combine configuration objects, parameter sets, or partial payloads into a single JSON object. Choose shallow when you want json2 to overwrite json1 at the top level; choose deep to recursively merge nested dictionaries. Feed the result to downstream nodes that require a unified JSON object.

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
<tr><td style="word-wrap: break-word;">json1</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First JSON object (or JSON string) to merge. If a string is provided, it will be parsed; invalid or empty strings become an empty object.</td><td style="word-wrap: break-word;">{'a': 1, 'b': {'x': 10}}</td></tr>
<tr><td style="word-wrap: break-word;">json2</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second JSON object (or JSON string) to merge. Values from json2 overwrite or merge into json1 depending on the mode. If a string is provided, it will be parsed; invalid or empty strings become an empty object.</td><td style="word-wrap: break-word;">{'b': {'y': 20}, 'c': 3}</td></tr>
<tr><td style="word-wrap: break-word;">merge_mode</td><td>True</td><td style="word-wrap: break-word;">["shallow", "deep"]</td><td style="word-wrap: break-word;">Select the merge strategy. 'shallow' overwrites top-level keys with json2. 'deep' recursively merges nested dictionaries; non-dict values are overwritten by json2.</td><td style="word-wrap: break-word;">deep</td></tr>
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
<tr><td style="word-wrap: break-word;">merged_data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The merged JSON object after applying the selected merge strategy.</td><td style="word-wrap: break-word;">{'a': 1, 'b': {'x': 10, 'y': 20}, 'c': 3}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Parsing behavior**: If json1 or json2 are strings, the node attempts to parse them as JSON. Invalid or empty strings become {} before merging.
- **Type requirements for merge**: Only dictionaries are merged. If either input is not a dictionary (e.g., a list or scalar), the node returns json2 as the result.
- **Shallow vs Deep**: Shallow merge applies {**json1, **json2}; deep merge recursively merges dictionary values, while arrays and scalars are overwritten by json2.
- **Error handling**: On merge errors, the node logs an error and returns json1 unchanged.
- **No array merging**: Arrays/lists are not merged element-wise; if encountered at any key, json2's value replaces json1's value.

## Troubleshooting
- **Output is just json2**: Ensure both inputs parse to dictionaries. If an input is a list, number, string (unparsed), or None, the node will return json2.
- **Fields not merging as expected**: Confirm 'merge_mode' is set to 'deep' for recursive merging. In 'shallow' mode, only top-level keys are considered and json2 overwrites json1.
- **Invalid JSON string inputs**: If providing strings, ensure they are valid JSON. Invalid strings are treated as {} which may lead to unexpected overwrites or missing data.
- **Nested arrays not combined**: This node does not merge arrays. If you need array concatenation or custom list merging, preprocess those keys before using this node.
