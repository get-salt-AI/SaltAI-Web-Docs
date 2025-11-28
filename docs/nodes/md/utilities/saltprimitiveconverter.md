# Primitive Value Converter

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts an input value into STRING, LIST, or DICT forms with optional element/value casting. It can parse plain strings into lists or dictionaries, extract a specific list item or dict value by index/key, and safely handles conversion errors with sensible defaults.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltprimitiveconverter.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to normalize data between primitives and collections. Typical flows include: converting a comma- or newline-delimited string into a typed list; turning key:value text into a dict; extracting a single element from a list/dict as a string; or re-casting list/dict elements to a specific primitive type.

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
<tr><td style="word-wrap: break-word;">input_value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to convert. Accepts strings, numbers, booleans, lists, or dicts. Strings can be comma-separated, key:value pairs, or multi-line content.</td><td style="word-wrap: break-word;">a: 1, b: 2</td></tr>
<tr><td style="word-wrap: break-word;">output_type</td><td>True</td><td style="word-wrap: break-word;">STRING (enum: STRING \| INT \| FLOAT \| BOOLEAN \| LIST \| DICT)</td><td style="word-wrap: break-word;">Target output type. Practical outputs are STRING, LIST, or DICT. When STRING is selected, the whole input is pretty-printed JSON unless index_or_key is provided to extract a single element.</td><td style="word-wrap: break-word;">LIST</td></tr>
<tr><td style="word-wrap: break-word;">sub_data_type</td><td>False</td><td style="word-wrap: break-word;">STRING (enum: ORIGIN \| STRING \| INT \| FLOAT \| BOOLEAN)</td><td style="word-wrap: break-word;">For LIST/DICT conversions, sets the element/value type after parsing. ORIGIN keeps values as-is; STRING/INT/FLOAT/BOOLEAN cast each element/value accordingly.</td><td style="word-wrap: break-word;">INT</td></tr>
<tr><td style="word-wrap: break-word;">index_or_key</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">When extracting from a LIST or DICT to STRING, provide the index (for lists) or key (for dicts). If set and valid, the node returns that single value cast to sub_data_type as a STRING.</td><td style="word-wrap: break-word;">2</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The converted value. Returns a STRING (element extraction or full pretty-printed JSON), a LIST (typed elements), or a DICT (typed values).</td><td style="word-wrap: break-word;">[1, 2, 3]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **STRING output behavior**: If index_or_key is empty and output_type=STRING, the node returns the entire input_value as pretty-printed JSON text.
- **Element extraction**: If index_or_key is provided and input_value is a LIST or DICT, the node extracts that element/value and returns it as STRING (cast using sub_data_type). Invalid indices/keys return an empty string.
- **Typing list/dict contents**: sub_data_type applies to parsed list items or dict values (e.g., cast to INT, FLOAT, BOOLEAN, or keep as ORIGIN).
- **String parsing to LIST**: A comma-separated string without any ':' in its parts is parsed into a list, with each item cast via sub_data_type.
- **String parsing to DICT**: For output_type=DICT, a string like "key: value, other: value" is parsed into a dict. Multi-line formats with 'key: value' per line are also supported.
- **Multi-line list support**: Lines beginning with '-' or 'N)' are treated as list items. Lines that look numeric are attempted as FLOAT.
- **Unsupported primitive outputs**: Although INT, FLOAT, and BOOLEAN are listed in output_type options, non-STRING primitive outputs are not emitted directly. To get a single primitive, extract using index_or_key with output_type=STRING and interpret the result, or produce a LIST/DICT of typed values.
- **Defaults on error**: On conversion errors, the node falls back to defaults: STRING="", INT=0, FLOAT=0.0, BOOLEAN=false, LIST=[], DICT={}.
- **ORIGIN sub_data_type**: Keeps each element/value unchanged in LIST/DICT outputs.

## Troubleshooting
- **Got empty string when extracting**: The index_or_key may be invalid (out of range or missing key). Verify the index/key and that input_value is a list/dict.
- **Expected a LIST but got empty list**: The input string may not match list parsing rules. Ensure it's comma-separated without ':' in items, or use multi-line with '-' or 'N)' prefixes.
- **Expected a DICT but got empty dict**: Ensure the input string contains 'key: value' pairs (comma-separated or one per line).
- **Booleans not casting as expected**: sub_data_type controls value casting; set sub_data_type=BOOLEAN. Ensure inputs are recognizable truthy/falsey strings (e.g., "true", "false", "yes", "no", "1", "0").
- **Need a single INT/FLOAT/BOOLEAN output**: Use output_type=STRING with index_or_key to extract a single element, and set sub_data_type to the desired type. The returned STRING will reflect the cast value.
- **Full object as STRING instead of element**: Provide index_or_key to extract a single element; otherwise STRING output pretty-prints the entire input.
