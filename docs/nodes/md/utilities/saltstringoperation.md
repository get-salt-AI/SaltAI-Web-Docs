# String Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs a wide range of string and list operations such as search, replace, case conversion, slicing, joining, splitting, generation, and validation. Behavior adapts based on the selected operation and on whether the input is a string or a list. Outputs include the transformed value and an operation-specific result (e.g., success flag, index, or length).

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltstringoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to transform or analyze strings and lists in your workflow: validate input (IS_ALPHA/IS_NUMERIC), find or count substrings (FIND/COUNT), reformat text (UPPERCASE/LOWERCASE/PROPERCASE/STRIP/TRIM_SPACES/REPLACE), split or join content (SPLIT/SPLIT_LINES/JOIN), repeat content (GENERATE), extract sections (SLICE/GET_LINE), or choose random values (RANDOM_INPUT/RANDOM_ELEMENT). Configure start_from_end and case_insensitive to change search direction and case handling when applicable.

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
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Primary value to process. Can be a string or a list depending on the operation.</td><td style="word-wrap: break-word;">Hello World</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Operation to perform. One of: COMPARE, CONCATENATE, COUNT, ENDS_WITH, EXTRACT_BETWEEN, FIND, GENERATE, GET_LINE, IS_ALPHA, IS_NUMERIC, JOIN, LENGTH, LOWERCASE, PROPERCASE, REPLACE, RANDOM_INPUT, RANDOM_ELEMENT, REVERSE, SLICE, SPLIT, SPLIT_LINES, STARTS_WITH, STRIP, TO_LIST, TO_STRING, TRIM_SPACES, UPPERCASE.</td><td style="word-wrap: break-word;">REPLACE</td></tr>
<tr><td style="word-wrap: break-word;">start_from_end</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, evaluates many operations from the end (reverse) of the input. Some operations internally reset this to ensure expected behavior.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">case_insensitive</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, applies case-insensitive matching where applicable (e.g., FIND, STARTS_WITH, ENDS_WITH, REPLACE, STRIP).</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">aux1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Auxiliary input 1. Purpose depends on the operation: e.g., search term (FIND/COUNT/STARTS_WITH/ENDS_WITH/SPLIT/REPLACE), joiner (JOIN), repeat count (GENERATE), index/line number (GET_LINE), or pop flag (RANDOM_ELEMENT). If param1 is non-empty, it overrides aux1.</td><td style="word-wrap: break-word;">,</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Auxiliary input 2. Purpose depends on the operation: e.g., start index (FIND), max splits (SPLIT), separator between repetitions (GENERATE), replacement string (REPLACE). If param2 is non-empty, it overrides aux2.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Auxiliary input 3. Purpose depends on the operation: e.g., end index (FIND), step (SLICE), replacement count (REPLACE; 0 means unlimited), or additional value for RANDOM_INPUT. If param3 is non-empty, it overrides aux3.</td><td style="word-wrap: break-word;">0</td></tr>
<tr><td style="word-wrap: break-word;">param1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">String parameter that, if non-empty, is used instead of aux1. Useful for inline constants.</td><td style="word-wrap: break-word;">world</td></tr>
<tr><td style="word-wrap: break-word;">param2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">String parameter that, if non-empty, is used instead of aux2.</td><td style="word-wrap: break-word;">-</td></tr>
<tr><td style="word-wrap: break-word;">param3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">String parameter that, if non-empty, is used instead of aux3.</td><td style="word-wrap: break-word;">3</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The processed value after applying the selected operation. Type matches the nature of the operation (string or list).</td><td style="word-wrap: break-word;">Hello-World</td></tr>
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Operation-specific result: may be a boolean (success), an integer (index or length), a list of indices, or other status depending on the operation.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Direction control**: If start_from_end is true, many operations evaluate from the end. Some operations reset this internally to preserve intuitive results. Final output may be reversed if the flag remains true after processing.
- **Case handling**: case_insensitive affects FIND, STARTS_WITH, ENDS_WITH, REPLACE, and STRIP/TRIM_SPACES.
- **Aux vs Param precedence**: If param1/param2/param3 are non-empty strings, they override aux1/aux2/aux3 respectively.
- **Type expectations**: RANDOM_ELEMENT expects a list; SPLIT and SPLIT_LINES expect a string; JOIN expects an iterable of strings; TO_LIST converts non-lists to lists of their elements.
- **REPLACE count**: aux3 is the replacement count; 0 is treated as unlimited.
- **FIND indexes**: aux2 is start index; aux3 is end index. With lists, returns per-element indices.
- **GET_LINE indexing**: For strings, aux1 is a 1-based line number. For lists, aux1 is a zero-based index (negative index allowed when evaluating from end).
- **GENERATE behavior**: aux1 is a positive repetition count; aux2 is the separator between repetitions (string or list element).
- **Error handling**: On invalid inputs or types, result is typically False (or -1 for FIND), and output may be unchanged or empty depending on the operation.

## Troubleshooting
- **SPLIT/SPLIT_LINES error: 'Input cannot be a List'**: Ensure the input is a string when using SPLIT or SPLIT_LINES.
- **RANDOM_ELEMENT error: 'expects a List'**: Provide a list as input. Optionally set aux1 to true to pop (remove) the chosen element from the list.
- **Unexpected case-sensitive results**: Toggle case_insensitive to true for case-insensitive matching in FIND/STARTS_WITH/ENDS_WITH/REPLACE.
- **FIND returns -1**: The substring was not found or types were incompatible. Check aux1 (search term) and optional indexes aux2/aux3.
- **REPLACE not limiting count**: Ensure aux3 is set to a positive integer. A value of 0 means unlimited replacements.
- **GET_LINE fails or empty output**: For strings, use a positive 1-based line number in aux1. For lists, ensure the index is within range.
- **JOIN fails**: Ensure input is an iterable of strings. Set aux1 to the desired join delimiter (use empty string for concatenation).
- **GENERATE invalid number**: aux1 must be a positive integer. For lists, aux2 may be used as a separator element between repetitions.
- **Direction confusion**: If results look reversed, check start_from_end. Some operations reset it to False internally; others honor it and reverse at the end.
