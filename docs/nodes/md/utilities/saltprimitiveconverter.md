# Primitive Value Converter

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts a given value between primitive and collection types. Supports STRING, INT, FLOAT, BOOLEAN, LIST, and DICT, with optional element casting for collection outputs and extraction via an index or key. Includes guardrails that fall back to sensible defaults when conversion fails.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltprimitiveconverter.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to normalize or reformat data between primitive types and collections. Common workflows include parsing user-entered text into lists or dictionaries, converting numbers to strings or booleans, extracting a specific element from a list/dict as a string, and preparing values to meet downstream node input requirements.

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
<tr><td style="word-wrap: break-word;">input_value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to convert. Can be a primitive (string, number, boolean) or a collection (list, dict). Strings can represent lists (comma-separated or line-bulleted) or dictionaries (key: value lines or comma-separated key:value pairs).</td><td style="word-wrap: break-word;">name: Alice, age: 30, height: 1.7</td></tr>
<tr><td style="word-wrap: break-word;">output_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The target type for conversion. One of: STRING, INT, FLOAT, BOOLEAN, LIST, DICT.</td><td style="word-wrap: break-word;">LIST</td></tr>
<tr><td style="word-wrap: break-word;">sub_data_type</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">When outputting LIST or DICT, specify how to cast elements/values. One of: ORIGIN (leave as-is), STRING, INT, FLOAT, BOOLEAN. Defaults to STRING in most operations.</td><td style="word-wrap: break-word;">INT</td></tr>
<tr><td style="word-wrap: break-word;">index_or_key</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">When output_type is STRING (or when sub_data_type is STRING and the input is a list/dict), you can extract a specific element by its zero-based index (for lists) or key (for dicts). If provided, returns the extracted element (as a string) instead of converting the entire structure.</td><td style="word-wrap: break-word;">age</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The converted value in the requested type. For STRING, returns a pretty-printed JSON representation unless index_or_key is provided, in which case it returns the extracted element as a string. For LIST/DICT, elements/values are cast according to sub_data_type.</td><td style="word-wrap: break-word;">["Alice", "Bob", "Charlie"]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **String parsing to LIST**: A comma-separated string like "a, b, c" becomes a list. Line-based lists are also supported if lines start with '-' or a number followed by ')'.
- **String parsing to DICT**: Works with comma-separated "key:value" pairs (e.g., "a:1, b:2") or multi-line "key: value" entries.
- **Element extraction**: If output_type is STRING (or sub_data_type is STRING with list/dict input) and index_or_key is set, the node returns only the specified element/key as a string.
- **Casting behavior for elements**: sub_data_type controls how list items or dict values are cast. Use ORIGIN to leave items unchanged.
- **Boolean casting**: Uses the platform's boolean string sanitizer (e.g., handling values like "true", "false", "yes", "no", "1", "0").
- **Fallback defaults**: On conversion errors, it returns defaults: STRING -> "", INT -> 0, FLOAT -> 0.0, BOOLEAN -> false, LIST -> [], DICT -> {}.
- **Integer casting**: INT will attempt to parse floats represented as strings by converting to float first and then to int.
- **STRING output formatting**: Without index_or_key, STRING output is a pretty-printed JSON representation of the input value.
- **DICT output nuances**: When output_type is DICT, the node returns the processed structure; ensure your input parses into a dict format for a true dict output.

## Troubleshooting
- **Got an empty string when extracting by index/key**: The provided index_or_key may be invalid or out of range. Ensure a valid zero-based index for lists or an existing key for dicts.
- **LIST output is empty**: The input string may not match expected list patterns (comma-separated or bulleted/numbered lines). Check the formatting or adjust sub_data_type.
- **Expected DICT but got a list**: Ensure the input text parses into a dictionary format (e.g., "key: value" lines or comma-separated key:value pairs). If the input doesn't parse as a dict, it may be treated as a list.
- **Boolean conversion doesn't match expectations**: Confirm the input values are recognizable boolean strings (e.g., "true/false", "yes/no", "1/0").
- **INT conversion failed**: Inputs like "12.5" are converted by parsing as float then casting to int. Non-numeric strings will fall back to defaults.
- **Index/key extraction not working**: Extraction only triggers for STRING output (or when sub_data_type is STRING and the input is a list/dict). Set output_type to STRING or set sub_data_type to STRING accordingly.
