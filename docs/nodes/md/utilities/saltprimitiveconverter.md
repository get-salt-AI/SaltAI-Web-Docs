# Primitive Value Converter

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node transforms an input value into a specified primitive type (STRING, INT, FLOAT, BOOLEAN, LIST, DICT) while handling a variety of input formats. It can parse text into lists or dictionaries, recast collection items to a uniform primitive type, and optionally extract a single element from lists or dicts when producing a string. This helps normalize mixed or textual data into predictable, strongly typed outputs for downstream nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltprimitiveconverter.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when data entering your workflow is not in the exact primitive type or structure required by the next step. Typical use cases include: converting model-generated bullet lists or CSV-like text into a LIST, transforming key:value text blocks into a DICT, converting numeric-looking strings into INT or FLOAT for numeric operations, and extracting a specific field from a list or dictionary as a STRING for display or routing. Place it after nodes that produce generic or string-based outputs (such as model output nodes or HTTP/API response nodes) and before nodes that need structured data, like list iterators, dictionary access nodes, conditional branches, or math/aggregation nodes.

A common workflow is: text-producing node → Primitive Value Converter (output_type=LIST or DICT, sub_data_type set as needed) → list/dict traversal or condition nodes. For LIST or DICT outputs, set sub_data_type to ensure that all elements have the intended primitive type (for example, FLOAT for scores or BOOLEAN for flags). When you only need one item from a collection, set index_or_key so this node returns just that element as a single primitive instead of the entire structure. Keep input formatting simple and consistent (commas, key:value, bullet lists) so parsing is deterministic.

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
<tr><td style="word-wrap: break-word;">input_value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to convert. Accepts strings, numbers, booleans, lists, and dicts. Strings can be parsed as comma-separated values, comma-separated key:value pairs, multi-line key:value entries (one per line), or bullet/numbered lists (for example, lines starting with '-' or '1)'). Existing lists and dicts are traversed and their elements or values are cast according to sub_data_type. When converting scalars to collection types, the scalar is wrapped into a list before further processing.</td><td style="word-wrap: break-word;">status: active attempts: 3 - validate email - send welcome message - log to CRM</td></tr>
<tr><td style="word-wrap: break-word;">output_type</td><td>True</td><td style="word-wrap: break-word;">STRING (one of: STRING, INT, FLOAT, BOOLEAN, LIST, DICT)</td><td style="word-wrap: break-word;">The target primitive type. STRING returns a text representation of the input; if index_or_key is empty, the entire input_value is pretty-printed as JSON-style text, and if index_or_key is set, only the selected element is cast and returned. LIST returns a list of values, parsed from text or from existing collection values. DICT returns a mapping built from key:value pairs or from an existing dict with its values recast. INT and FLOAT perform numeric conversions, and BOOLEAN converts to true/false using a boolean sanitizer.</td><td style="word-wrap: break-word;">LIST</td></tr>
<tr><td style="word-wrap: break-word;">sub_data_type</td><td>False</td><td style="word-wrap: break-word;">STRING (one of: ORIGIN, STRING, INT, FLOAT, BOOLEAN)</td><td style="word-wrap: break-word;">Controls the type of individual elements or values, mainly when the output is LIST or DICT or when parsing strings into collections. ORIGIN keeps the original element type. STRING forces elements to strings. INT and FLOAT convert numeric-like content to numbers, and BOOLEAN uses a sanitizer that interprets common boolean-like strings (such as 'true', 'false', 'yes', 'no', '1', '0'). This ensures consistent typing inside parsed or recast collections.</td><td style="word-wrap: break-word;">FLOAT</td></tr>
<tr><td style="word-wrap: break-word;">index_or_key</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional index (for list inputs) or key (for dict inputs) used to extract a single element, mainly when producing STRING output or when sub_data_type is STRING on collections. If non-empty, the node attempts to retrieve that index or key from input_value and returns only that element, cast via sub_data_type. If empty and output_type is STRING, the node instead returns a formatted JSON string of the entire input_value.</td><td style="word-wrap: break-word;">attempts</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The converted value in the requested primitive type. For STRING, it is either a JSON-style string of the entire structure or a single extracted element as a primitive. For LIST, it is a list of elements cast according to sub_data_type, derived from parsed text or from existing list/dict content. For DICT, it is a mapping of keys to values cast according to sub_data_type, derived from structured text or existing dictionaries. For INT, FLOAT, and BOOLEAN, it is a single primitive value.</td><td style="word-wrap: break-word;">{'status': 'active', 'attempts': 3, 'steps': ['validate email', 'send welcome message', 'log to CRM']}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Large inputs (long multi-line text, large lists, or big dictionaries) require scanning and per-element casting, which can increase processing time. For very large payloads, consider pre-filtering or chunking the data before converting.
- **Limitations**: Text parsing is based on simple patterns (commas, 'key: value' lines, bullet markers). Complex formatting, nested structures, or inconsistent delimiters may not parse cleanly and can result in an empty list/dict or in fallback behavior where the node treats segments as standalone values.
- **Behavior**: When output_type is STRING and index_or_key is empty, the node always returns a formatted JSON representation of the full input_value, ignoring sub_data_type. When index_or_key is provided but invalid (for example, list index out of range, dict key missing, or wrong type), the node logs a warning and returns an empty string as a safe default.
- **Behavior**: Non-string, non-collection inputs converted to collection types are wrapped in a one-element list and then cast according to sub_data_type. For DICT output, if text input lacks recognizable key:value pairs, the node cannot form a meaningful dictionary and will rely on default values or alternative parsing paths.
- **Limitations**: INT conversion from string values that contain decimals works by converting to float then to int, effectively truncating the decimal part. If you need a specific rounding strategy, perform explicit numeric operations in another node after this conversion.

## Troubleshooting
- **Element extraction returns empty string**: If using index_or_key produces an empty string, the index might be out of bounds or the key does not exist. First inspect the entire structure by setting output_type to STRING with index_or_key left empty, then adjust the index or key based on the displayed structure.
- **Output is [] or {} when parsing text**: If a text block converted to LIST or DICT returns an empty list or dict, the text likely does not match expected patterns (comma-separated items, 'key: value' lines, or bullet lists). Reformat the text with clear separators and simple structure, then reconvert.
- **Numeric output unexpectedly 0 or 0.0**: When converting to INT or FLOAT, non-numeric characters or irregular formats can cause the conversion to fail and fall back to defaults. Clean the input (remove units, labels, or symbols) so that it contains only numeric characters and optional decimal points.
- **Boolean results are not as expected**: If BOOLEAN conversion yields unexpected values, your input strings may not match the sanitizer’s recognized truthy/falsey variants. Normalize inputs to standard forms such as 'true', 'false', 'yes', 'no', '1', or '0', or choose sub_data_type ORIGIN when you need to preserve the original values inside a collection.
