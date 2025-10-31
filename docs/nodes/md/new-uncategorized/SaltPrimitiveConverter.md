# Primitive Value Converter

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts an input value into a specified primitive type: STRING, INT, FLOAT, BOOLEAN, LIST, or DICT. Supports parsing of simple list/dict strings, optional casting of elements inside collections, and targeted extraction by index or key when outputting a single string. Includes safe fallbacks and formatting to reduce errors during data preparation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/SaltPrimitiveConverter.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to normalize or transform incoming data into a specific primitive type for downstream nodes. Typical flows include: turning user or tool outputs into structured lists/dicts, coercing numeric or boolean values, extracting a single element from a list/dict as a string, or pretty-printing any input as a JSON string.

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
<tr><td style="word-wrap: break-word;">input_value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to convert. Accepts any type, including strings, numbers, booleans, lists, and dicts. Strings can represent comma-separated lists or key:value pairs.</td><td style="word-wrap: break-word;">a:1, b:2, c:3</td></tr>
<tr><td style="word-wrap: break-word;">output_type</td><td>True</td><td style="word-wrap: break-word;">ENUM[STRING\|INT\|FLOAT\|BOOLEAN\|LIST\|DICT]</td><td style="word-wrap: break-word;">Target type for the conversion. Determines the final shape of the output.</td><td style="word-wrap: break-word;">LIST</td></tr>
<tr><td style="word-wrap: break-word;">sub_data_type</td><td>False</td><td style="word-wrap: break-word;">ENUM[ORIGIN\|STRING\|INT\|FLOAT\|BOOLEAN]</td><td style="word-wrap: break-word;">For LIST/DICT outputs and for element extraction, controls how individual elements/values are cast. ORIGIN preserves original types.</td><td style="word-wrap: break-word;">INT</td></tr>
<tr><td style="word-wrap: break-word;">index_or_key</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">When extracting a specific element to STRING output: provide a zero-based index for lists or a key for dicts. If omitted and output_type is STRING, the entire input is returned as a formatted JSON string.</td><td style="word-wrap: break-word;">0</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The converted value in the requested type. For STRING outputs without index_or_key, the whole input is JSON-formatted. For LIST/DICT outputs, elements/values can be cast using sub_data_type.</td><td style="word-wrap: break-word;">[1, 2, 3]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Index or key extraction applies when output_type is STRING. If provided, the node attempts to extract that element from a list/dict and cast it using sub_data_type.
- If index_or_key is empty and output_type is STRING, the entire input is returned as a pretty JSON string.
- For LIST/DICT outputs, sub_data_type controls casting of elements/values. Use ORIGIN to preserve original types.
- String parsing supports simple CSV lists (e.g., "a,b,c") and comma-separated key:value pairs (e.g., "a:1, b:2"). It also understands basic multiline formats like "key: value" per line or list items prefixed with '-' or '1)'.
- On conversion errors (e.g., bad index/key or incompatible casting), the node returns a safe default for the target type (e.g., empty string for STRING, [] for LIST, {} for DICT) and logs a warning.
- Boolean casting follows a sanitizer that interprets common truthy/falsey string forms.

## Troubleshooting
- Getting an empty string when extracting from a list/dict: Verify index_or_key is valid (0-based index for lists, existing key for dicts) and that output_type is STRING.
- LIST/DICT output not as expected: Check sub_data_type to ensure elements are being cast correctly (e.g., set to INT for numeric lists).
- Parsed list/dict is empty from a string: Ensure the input string uses supported formats (comma-separated items, key:value pairs, or multiline with '-' or 'key: value').
- Unexpected numeric conversion results: If input values contain decimals and you selected INT, they are truncated by casting via float first. Choose FLOAT if you need decimals.
- Boolean not converting: Ensure your input uses recognizable truthy/falsey representations (e.g., true/false, yes/no, 1/0).
