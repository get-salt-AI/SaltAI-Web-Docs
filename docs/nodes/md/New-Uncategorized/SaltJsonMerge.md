# JSON: Merge

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Merges two JSON objects into a single result. Supports shallow merge (second object overwrites first at the top level) and deep merge (recursively combines nested objects while letting the second object take precedence on conflicts). Accepts either native JSON objects or JSON strings.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/SaltJsonMerge.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to combine two JSON sources, such as layering user overrides over defaults, merging configuration fragments, or consolidating API response sections. Choose shallow merge for simple top-level overwrites, or deep merge to recursively combine nested dictionaries.

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
<tr><td style="word-wrap: break-word;">json1</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The base JSON data to merge into. Can be a JSON object or a JSON string that parses to an object.</td><td style="word-wrap: break-word;">{'default': {'theme': 'light', 'features': {'beta': False}}}</td></tr>
<tr><td style="word-wrap: break-word;">json2</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The JSON data applied over json1. Its values take precedence on conflicts. Can be a JSON object or a JSON string that parses to an object.</td><td style="word-wrap: break-word;">{'override': {'theme': 'dark', 'features': {'beta': True}}}</td></tr>
<tr><td style="word-wrap: break-word;">merge_mode</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Merge strategy to apply. shallow: top-level keys from json2 overwrite json1. deep: recursively merge nested objects; json2 still takes precedence on conflicts.</td><td style="word-wrap: break-word;">deep</td></tr>
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
<tr><td style="word-wrap: break-word;">merged_data</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The merged JSON object after applying the selected merge strategy.</td><td style="word-wrap: break-word;">{'theme': 'dark', 'features': {'beta': True}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Order matters**: json2 overwrites or augments json1 where keys overlap.
- **Input flexibility**: Both json1 and json2 may be JSON objects or JSON strings. Strings are parsed automatically.
- **Type requirements**: If either input does not resolve to an object (dictionary), the node returns json2 as-is.
- **Deep merge specifics**: Only nested objects (dictionaries) are merged recursively. Arrays and non-object types are overwritten by json2.
- **Shallow merge behavior**: Only top-level keys are combined; nested structures are not merged recursively.
- **Parsing fallback**: Invalid or empty JSON strings are treated as empty objects during parsing.

## Troubleshooting
- **Merged result equals json2**: One or both inputs were not objects after parsing. Ensure both are JSON objects (e.g., `{}`) rather than arrays, numbers, or strings.
- **Nested values not merging**: Confirm merge_mode is set to "deep". Shallow mode only merges top-level keys.
- **Arrays not merged as expected**: Deep merge does not combine arrays; the array from json2 replaces json1's array for the same key.
- **Invalid JSON string input**: If a string fails to parse, it is treated as `{}`. Validate your JSON strings beforehand to avoid unexpected defaults.
- **Unexpected overwrites**: In deep merges, when types conflict (e.g., dict vs string), json2's value replaces json1's. Verify your data types for overlapping keys.
