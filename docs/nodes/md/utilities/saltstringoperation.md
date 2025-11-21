# String Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs a wide range of string and list operations with a single, configurable node. It supports searching, slicing, case conversion, splitting, joining, replacement, comparisons, random selection, and more. Behavior can be adjusted to evaluate from the end and/or ignore case for supported operations.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltstringoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to transform or analyze text or lists in your workflow. Select the desired operation and optionally supply auxiliary values or parameters to control delimiters, counts, indexes, or modes. It is often used for preparing prompts, parsing outputs, formatting text, deduping or extracting substrings, or manipulating lists.

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
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Primary value to process. Accepts a string or a list (depending on the operation). For string-focused operations, pass a string; for list-focused operations, pass a list.</td><td style="word-wrap: break-word;">Hello World</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">The operation to perform. One of: COMPARE, CONCATENATE, COUNT, ENDS_WITH, EXTRACT_BETWEEN, FIND, GENERATE, GET_LINE, IS_ALPHA, IS_NUMERIC, JOIN, LENGTH, LOWERCASE, PROPERCASE, REPLACE, RANDOM_INPUT, RANDOM_ELEMENT, REVERSE, SLICE, SPLIT, SPLIT_LINES, STARTS_WITH, STRIP, TO_LIST, TO_STRING, TRIM_SPACES, UPPERCASE.</td><td style="word-wrap: break-word;">REPLACE</td></tr>
<tr><td style="word-wrap: break-word;">start_from_end</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, evaluates certain operations from the end of the input. For strings this generally reverses before processing and re-reverses after, altering how starts/ends/find/slice behave.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">case_insensitive</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, uses case-insensitive matching for supported operations (e.g., STARTS_WITH, ENDS_WITH, FIND, REPLACE, STRIP).</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">aux1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Auxiliary Input 1. Purpose varies by operation (e.g., pattern/substring, repetition count, delimiter, index). Overridden by param1 if provided.</td><td style="word-wrap: break-word;">,</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Auxiliary Input 2. Purpose varies by operation (e.g., replacement text, max splits, start index, separator). Overridden by param2 if provided.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Auxiliary Input 3. Purpose varies by operation (e.g., end index, step, max replacements). Overridden by param3 if provided.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">param1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">String parameter used instead of aux1 if not empty. Useful for quickly entering text (e.g., delimiters, substrings) without wiring aux1.</td><td style="word-wrap: break-word;">world</td></tr>
<tr><td style="word-wrap: break-word;">param2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">String parameter used instead of aux2 if not empty.</td><td style="word-wrap: break-word;">-</td></tr>
<tr><td style="word-wrap: break-word;">param3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">String parameter used instead of aux3 if not empty.</td><td style="word-wrap: break-word;">2</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The transformed value after applying the selected operation. Type may be string or list, depending on the operation.</td><td style="word-wrap: break-word;">Hello-World</td></tr>
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Operation-specific result or status. Can be a boolean, an index/length, a list of indices, or other contextual value.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Operations vary in what they expect: some require strings (e.g., SPLIT, SPLIT_LINES, case transforms), while others work on lists (e.g., RANDOM_ELEMENT, TO_LIST, JOIN on list).
- Parameters override auxiliaries: if param1/param2/param3 are non-empty, they supersede aux1/aux2/aux3 respectively.
- start_from_end reverses evaluation, affecting how starts/ends/find/slice behave. The node re-reverses the output to preserve original orientation unless the operation explicitly disables it.
- case_insensitive applies only to operations that support comparisons/searches (e.g., STARTS_WITH, ENDS_WITH, FIND, REPLACE, STRIP).
- SPLIT and SPLIT_LINES throw an error if the input is a list. They require a string input.
- RANDOM_ELEMENT requires a list input. If aux1 is truthy, the selected element is removed (pop) from the list; otherwise it is only read.
- GET_LINE: for strings, lines are 1-based indexed; for lists, indexing is 0-based and accepts start_from_end behavior with negative indexing.
- REPLACE: aux3 is the max replacement count; 0 means 'no limit' and is treated as unlimited.
- FIND: returns an index (or a list of indices for list input). With start_from_end on strings, it uses rfind. aux2 and aux3 serve as start/end indexes for the search.
- TO_LIST: converts a string to a list of characters; if input is already a list it is returned as-is.
- TO_STRING: returns the string form of input (the node internally converts input to string for processing).
- GENERATE: repeats the input value aux1 times; aux2 can act as a separator between repetitions; works for both strings and lists.
- STRIP vs TRIM_SPACES: TRIM_SPACES always strips spaces; STRIP strips the characters provided in aux1 (applies to each item if input is a list).

## Troubleshooting
- Operation fails or returns False: Verify the input type matches the operation's requirements (e.g., SPLIT expects a string; RANDOM_ELEMENT expects a list).
- Unexpected case sensitivity: Ensure case_insensitive is set to true for comparisons or searches if needed.
- Indexes out of range: For GET_LINE, ensure the index is valid (strings use 1-based lines; lists use 0-based indexes; start_from_end changes which element is targeted).
- No change after REPLACE: Confirm aux1 (pattern) and aux2 (replacement) are correct, and check aux3 (max count). For case-insensitive replacement, set case_insensitive to true.
- JOIN fails: Ensure input is a list of strings and aux1 is the join delimiter. Non-string elements can cause failure.
- GENERATE error: aux1 must be a positive integer. For lists, aux2 as a separator should be a string or compatible element.
- FIND returns -1: The substring/pattern was not found; check case_insensitive, start/end indexes (aux2/aux3), and start_from_end behavior.
- SPLIT returns unexpected count: Check delimiter in aux1 (or param1) and max splits aux2; note that 0 is treated as unlimited splits.
