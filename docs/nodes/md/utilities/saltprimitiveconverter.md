# Primitive Value Converter

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts an input value to a target primitive/container type with flexible parsing and safe fallbacks. Supports converting to STRING, LIST, and DICT, with optional element/value casting via a sub-data-type. You can also extract a specific element from lists or dicts by index/key when producing a STRING.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltprimitiveconverter.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to normalize or reshape incoming data into strings, lists, or dictionaries for downstream nodes. Typical flows include: parsing user-entered text into a list or dict, extracting a single value from a list/dict as a string, or recasting elements within lists/dicts to a specific primitive type.

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
<tr><td style="word-wrap: break-word;">input_value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Any value to convert. May be a string, number, boolean, list, or dictionary. Strings can be parsed into lists or dicts using simple patterns.</td><td style="word-wrap: break-word;">apples, bananas, oranges</td></tr>
<tr><td style="word-wrap: break-word;">output_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Target type for the output. Options: STRING, INT, FLOAT, BOOLEAN, LIST, DICT. STRING produces a pretty-printed JSON string or a single extracted string; LIST/DICT produce parsed/converted containers.</td><td style="word-wrap: break-word;">LIST</td></tr>
<tr><td style="word-wrap: break-word;">sub_data_type</td><td>False</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Element/value casting type when producing LIST/DICT or when extracting a single value into STRING. Options: ORIGIN (no change), STRING, INT, FLOAT, BOOLEAN.</td><td style="word-wrap: break-word;">INT</td></tr>
<tr><td style="word-wrap: break-word;">index_or_key</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For LIST/DICT inputs when output_type is STRING (or when sub_data_type is STRING), specify a list index (e.g., 0) or a dict key to extract a single element as a string.</td><td style="word-wrap: break-word;">0</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The converted value. For STRING, returns a pretty-printed JSON string or an extracted single value. For LIST/DICT, returns a parsed/converted container based on input and sub_data_type.</td><td style="word-wrap: break-word;">['apples', 'bananas', 'oranges']</td></tr>
</tbody>
</table>
</div>

## Important Notes
- STRING output behavior: without index_or_key, the node returns a pretty-printed JSON string of the input; with index_or_key on list/dict inputs, it returns the extracted value cast to sub_data_type (commonly STRING).
- String parsing to LIST: a comma-separated string like "a, b, c" is parsed into a list. Elements are cast using sub_data_type.
- String parsing to DICT: if output_type is DICT and the input is comma-separated key:value pairs (e.g., "k1:v1, k2:v2"), it parses into a dict and casts values using sub_data_type.
- Multiline string parsing: lines matching "key: value" form a dict; lines starting with '-' or '1)' form a list; standalone numeric lines may be parsed as floats.
- When converting existing dicts/lists, their values/elements are cast to the chosen sub_data_type.
- Invalid index/key extraction logs a warning and returns an empty string for STRING output.
- If conversion fails, the node falls back to a sensible default for the requested type (e.g., empty list or dict).
- Direct scalar outputs for INT/FLOAT/BOOLEAN are not typically produced; if specified, the node may default to returning list-like results rather than a single scalar. Prefer STRING for single-value extraction or use LIST/DICT outputs with sub_data_type for element casting.

## Troubleshooting
- Extraction returns empty string: Ensure index_or_key is valid for the given input (0-based index for lists, exact key for dicts) and that output_type is STRING when extracting.
- Output is a list instead of a single scalar: This node primarily produces STRING, LIST, or DICT. To get a single scalar, use STRING with index_or_key to extract a specific element, or handle scalar casting downstream.
- Parsed dict is empty: Confirm your input string uses 'key: value' format on lines or 'key:value' pairs separated by commas when output_type is DICT.
- Values not in desired type: Set sub_data_type to STRING/INT/FLOAT/BOOLEAN so elements/values are cast during parsing or conversion.
- Comma-separated text became a list, not a dict: For dict parsing from comma-separated text, include ':' in each pair and set output_type to DICT (e.g., "a:1, b:2").
