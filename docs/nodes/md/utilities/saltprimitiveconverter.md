# Primitive Value Converter

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts an input value between primitive data types (STRING, INT, FLOAT, BOOLEAN, LIST, DICT). Supports element/value casting for collections via a sub-data-type, and optional extraction of a specific element by index or key. Provides safe conversion with sensible defaults and basic parsing for comma-separated and multi-line strings.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltprimitiveconverter.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to normalize or transform data between types before passing it to other nodes. Typical workflows include: turning comma-separated strings into lists, parsing simple key:value strings into dicts, casting list elements to a desired type, pretty-printing any value as a JSON string, or extracting a specific item from a list/dict by index/key.

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
<tr><td style="word-wrap: break-word;">input_value</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to convert. Can be a string, number, boolean, list, or dict. Strings may be parsed as lists (comma-separated) or dicts (comma-separated key:value pairs, or multi-line key: value).</td><td style="word-wrap: break-word;">name: Alice, age: 30</td></tr>
<tr><td style="word-wrap: break-word;">output_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target output type. Options: STRING, INT, FLOAT, BOOLEAN, LIST, DICT.</td><td style="word-wrap: break-word;">LIST</td></tr>
<tr><td style="word-wrap: break-word;">sub_data_type</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">When producing LIST or DICT (or when casting elements/values), specifies the type for elements/values. Options: ORIGIN, STRING, INT, FLOAT, BOOLEAN. ORIGIN keeps original element types.</td><td style="word-wrap: break-word;">INT</td></tr>
<tr><td style="word-wrap: break-word;">index_or_key</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For list/dict extraction or when output_type is STRING: provide a zero-based index (for lists) or a key (for dicts) to extract a single item. If set, the node returns only that item (cast according to sub_data_type).</td><td style="word-wrap: break-word;">0</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The converted value in the specified output type. For STRING output without index_or_key, returns a pretty-printed JSON string representation of the input. For LIST output, returns a list (dicts become list of values). For DICT output, returns a dict if parsing or casting produced one.</td><td style="word-wrap: break-word;">[1, 2, 3]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **STRING output behavior**: If output_type is STRING and no index_or_key is provided, the node returns a pretty-printed JSON string of the original input value.
- **Element extraction**: If index_or_key is provided and the input is a list/dict, the node returns only that element (list index or dict key). On failure, it returns an empty string.
- **Sub-data-type casting**: Elements and values in lists/dicts are cast using sub_data_type. Use ORIGIN to keep original types.
- **String parsing**: Comma-separated strings without ':' are parsed as lists; comma-separated 'key:value' pairs (when output_type is DICT) are parsed into dicts; multi-line strings can be parsed as key: value dict entries or as list items (lines starting with '-' or 'n)').
- **LIST from dicts**: When output_type is LIST and the processed input is a dict, only the dict's values are returned (keys are discarded).
- **Error handling**: If conversion fails, the node falls back to default base values per type (STRING -> "", INT -> 0, FLOAT -> 0.0, BOOLEAN -> false, LIST -> [], DICT -> {}).
- **Unsupported types**: If an unsupported output_type is provided (anything other than STRING, LIST, DICT), the node logs a warning and defaults to LIST behavior.

## Troubleshooting
- **Getting an empty string output**: If using index_or_key on a list/dict and the index is out of range or the key doesn't exist, the node returns an empty string. Verify the index/key and data shape.
- **Unexpected list from a dict**: When output_type is LIST, dict inputs become a list of values (keys are removed). If you need key-value pairs, use DICT output.
- **String not parsed into dict**: Comma-separated key:value parsing only occurs when output_type is DICT. Ensure output_type is DICT and the string uses 'key: value' pairs separated by commas.
- **Numbers not converting as expected**: If element casting to INT/FLOAT fails for some items, those items may remain uncast (ORIGIN) or trigger defaults during processing. Check input formatting and consider using sub_data_type = STRING or ORIGIN as needed.
- **Got a JSON string instead of structured data**: STRING output without index_or_key always returns a JSON-formatted string. Choose LIST or DICT output to get structured results, or set index_or_key to extract a single item as a primitive.
