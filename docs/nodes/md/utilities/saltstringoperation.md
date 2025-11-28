# String Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs a wide range of string and list operations on the provided input. Supports concatenation, search, replace, case transforms, slicing, splitting, validation, randomization, and more. Many operations optionally honor start-from-end evaluation and case-insensitive matching.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltstringoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to transform or analyze strings or lists within a workflow. Select an operation and provide auxiliary arguments as needed via aux inputs or param text fields. The node returns the transformed output and an operation-specific result (such as boolean success, index, count, or length).

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
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Operation to perform. One of: COMPARE (not implemented), CONCATENATE, COUNT, ENDS_WITH, EXTRACT_BETWEEN (not implemented), FIND, GENERATE, GET_LINE, IS_ALPHA, IS_NUMERIC, JOIN, LENGTH, LOWERCASE, PROPERCASE, REPLACE, RANDOM_INPUT, RANDOM_ELEMENT, REVERSE, SLICE, SPLIT, SPLIT_LINES, STARTS_WITH, STRIP, TO_LIST, TO_STRING, TRIM_SPACES, UPPERCASE.</td><td style="word-wrap: break-word;">REPLACE</td></tr>
<tr><td style="word-wrap: break-word;">start_from_end</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, evaluates certain operations from the end of the input. After processing, the final output is reversed back so the visual output reads normally.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">case_insensitive</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, operations that support it (e.g., FIND, STARTS_WITH, ENDS_WITH, REPLACE, STRIP/TRIM_SPACES) perform case-insensitive matching.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">aux1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Auxiliary input 1. Purpose depends on the selected operation. Overridden by param1 if param1 is not empty.</td><td style="word-wrap: break-word;">,</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Auxiliary input 2. Purpose depends on the selected operation. Overridden by param2 if param2 is not empty.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Auxiliary input 3. Purpose depends on the selected operation. Overridden by param3 if param3 is not empty.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">param1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Text parameter 1. If non-empty, it will be used instead of aux1 for the operation.</td><td style="word-wrap: break-word;">world</td></tr>
<tr><td style="word-wrap: break-word;">param2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Text parameter 2. If non-empty, it will be used instead of aux2 for the operation.</td><td style="word-wrap: break-word;">planet</td></tr>
<tr><td style="word-wrap: break-word;">param3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Text parameter 3. If non-empty, it will be used instead of aux3 for the operation.</td><td style="word-wrap: break-word;">2</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The processed value (string or list), depending on operation. For many operations this is a transformed version of the input; for some, it may remain unchanged.</td><td style="word-wrap: break-word;">Hello planet</td></tr>
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Operation-specific result such as boolean success, index, count, or length. See notes below per operation.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Operation-specific behavior**:
- - RANDOM_INPUT: Picks randomly from [input, aux1, aux2, aux3] that are provided. Result is the chosen index; output is the chosen value.
- - RANDOM_ELEMENT: Requires a list input. Result is a random index; output is the selected element. If aux1 is truthy, the element is removed (pop) from the list.
- - CONCATENATE: Appends aux values (strings or lists) to input. For lists, concatenates lists; for strings, concatenates strings. The order respects start_from_end.
- - JOIN: Joins elements of a list using aux1 as the separator. Result is True on success.
- - GENERATE: Repeats the input i times (aux1 > 0). For strings, optionally inserts aux2 between repetitions. For lists, extends by the list repeatedly and can insert aux2 as a separator when aux2 is a string. Throws if aux1 <= 0.
- - LENGTH: Result is len(input).
- - COUNT: Result is input.count(aux1) when supported; 0 on type mismatch.
- - STARTS_WITH / ENDS_WITH: For strings, checks prefix/suffix; for lists, checks the first (or last when start_from_end) element's string form. Honors case_insensitive.
- - TO_LIST: Converts input to a list (list(input)) if not already a list. Result is True.
- - TO_STRING: Converts input to string implicitly (output is the string form established at start); result is True.
- - IS_NUMERIC: Result True if input can be parsed as float, else False.
- - IS_ALPHA: Result True if str(input).isalpha(), else False.
- - GET_LINE: For strings, returns the i-th line (1-based) via splitlines; for lists, returns the element at index i (or from end if start_from_end). i must be positive for strings.
- - STRIP / TRIM_SPACES: Strips characters (aux1; space for TRIM_SPACES) from string(s). For lists, applies per element. With case_insensitive, strips lower/upper variants too. Result is length of output.
- - REPLACE: Replaces aux1 with aux2. aux3 is count (0 treated as unlimited). Supports case_insensitive via regex when True. Works on strings or each element of a list.
- - REVERSE: Reverses the input string, or reverses the list order.
- - SLICE: Uses Python slicing with aux1:start, aux2:stop, aux3:step. Result is length of the sliced output.
- - FIND: Returns index of aux1 within the string (or per-element for lists). With start_from_end uses rfind; case_insensitive supported. Returns -1 on error.
- - SPLIT: Splits a string on delimiter aux1 with optional maxsplit aux2 (0 means unlimited). Result is number of parts. Not allowed for list input.
- - SPLIT_LINES: Splits a string into lines. Result is number of lines. Not allowed for list input.
- **Parameter precedence**: If param1/param2/param3 are non-empty strings, they override aux1/aux2/aux3 respectively.
- **start_from_end semantics**: Some operations reverse the evaluation perspective. After processing, the node reverses the final output back so the returned output reads naturally.
- **Type expectations**: Some operations require a specific type (e.g., RANDOM_ELEMENT needs a list; JOIN expects an iterable of strings). Mismatches may set result to a default (e.g., 0 or -1) or raise an exception.
- **Case-insensitive matching**: Implemented for FIND, STARTS_WITH, ENDS_WITH, REPLACE, and STRIP/TRIM_SPACES.
- **COMPARE and EXTRACT_BETWEEN**: Listed operations may not be implemented in this version; selecting them may have no effect or cause failure.
- **Error handling**: On unexpected errors, result may be False and output may be unchanged or partially processed.

## Troubleshooting
- **SPLIT/SPLIT_LINES on a list**: The node raises an error if input is a list. Convert to string first or adjust the operation.
- **RANDOM_ELEMENT with non-list input**: Fails with an exception. Ensure the input is a list.
- **GET_LINE index issues**: For strings, aux1 must be a positive integer (1-based). For lists, negative values are not supported except when using start_from_end, which internally handles the reverse indexing.
- **JOIN type errors**: Ensure input is a list of strings (or values convertible to strings). Non-iterables or mixed types may fail; consider converting elements to strings first.
- **REPLACE count of 0**: Count 0 is treated as unlimited replacements internally. Use a positive number to limit replacements.
- **IS_NUMERIC false negatives**: Inputs with thousands separators or non-standard formats may not parse with float(). Pre-clean your input.
- **Case-insensitive operations**: If case_insensitive doesn't appear to work, verify that the selected operation supports it (not all do).
- **Empty aux vs param precedence**: If aux inputs seem ignored, check if param1/2/3 are set; non-empty params override aux values.
- **Unexpected reversed output**: start_from_end affects evaluation and final output reversal. If output looks reversed, ensure start_from_end is set appropriately.
