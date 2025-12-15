# String Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs a wide range of string and list manipulations based on a selected operation. Supports comparisons, searching, slicing, transformations (case change, strip/trim, replace), splitting/joining, random selection, and generation/repetition. Many operations accept auxiliary inputs for patterns, indexes, counts, and separators, with optional text parameters that can override auxiliary inputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltstringoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to transform or analyze textual data or lists of strings as part of a workflow. Choose an operation and provide the required inputs: for example, use FIND to locate a substring, REPLACE to substitute text, SPLIT to break a string into a list, JOIN to glue a list into a string, or RANDOM_ELEMENT to pick an item from a list. The node returns the processed value (output) and a secondary result indicating success, counts, indexes, or lengths depending on the operation.

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
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Primary value to process. Can be a string or a list (commonly a list of strings).</td><td style="word-wrap: break-word;">Hello World</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Operation to perform. One of: COMPARE, CONCATENATE, COUNT, ENDS_WITH, EXTRACT_BETWEEN, FIND, GENERATE, GET_LINE, IS_ALPHA, IS_NUMERIC, JOIN, LENGTH, LOWERCASE, PROPERCASE, REPLACE, RANDOM_INPUT, RANDOM_ELEMENT, REVERSE, SLICE, SPLIT, SPLIT_LINES, STARTS_WITH, STRIP, TO_LIST, TO_STRING, TRIM_SPACES, UPPERCASE.</td><td style="word-wrap: break-word;">FIND</td></tr>
<tr><td style="word-wrap: break-word;">start_from_end</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, many operations evaluate from the end. For strings this typically uses the reversed string; for lists it may use the last element or reverse before processing. Many operations will reset this flag internally after handling.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">case_insensitive</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, supported operations perform case-insensitive matching (e.g., FIND/STARTS_WITH/ENDS_WITH/REPLACE/COUNT/STRIP variants).</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">aux1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Auxiliary input 1. Meaning depends on operation (e.g., pattern/substring, repeat count, separator, index). Overridden by param1 if provided.</td><td style="word-wrap: break-word;">World</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Auxiliary input 2. Meaning depends on operation (e.g., replacement text, index, max splits, slice stop/step). Overridden by param2 if provided.</td><td style="word-wrap: break-word;">Universe</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Auxiliary input 3. Meaning depends on operation (e.g., replace count, slice step). Overridden by param3 if provided.</td><td style="word-wrap: break-word;">1</td></tr>
<tr><td style="word-wrap: break-word;">param1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional text parameter that, if not empty, replaces aux1 for the operation.</td><td style="word-wrap: break-word;">hello</td></tr>
<tr><td style="word-wrap: break-word;">param2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional text parameter that, if not empty, replaces aux2 for the operation.</td><td style="word-wrap: break-word;">, </td></tr>
<tr><td style="word-wrap: break-word;">param3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional text parameter that, if not empty, replaces aux3 for the operation.</td><td style="word-wrap: break-word;">3</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The processed value after applying the operation. Type depends on the operation (string or list).</td><td style="word-wrap: break-word;">Hello Universe</td></tr>
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Operation-specific result: may be True/False (success), an integer (index, length, count), or a list of integers for list-wise searches.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Parameter override**: If param1/param2/param3 are non-empty strings, they override aux1/aux2/aux3 respectively.
- **Start-from-end behavior**: When start_from_end is true, strings are typically processed in reverse and may be reversed back after processing. Many operations explicitly reset this flag internally to avoid double reversing.
- **List vs String**: Some operations expect specific types. RANDOM_ELEMENT requires a list. SPLIT and SPLIT_LINES require a string and will raise an error if a list is provided.
- **Case-insensitive matching**: When case_insensitive is true, supported operations use case-insensitive logic (e.g., regex IGNORECASE for REPLACE, lowercased comparisons for FIND/STARTS_WITH/ENDS_WITH).
- **GET_LINE indexing**: For strings, GET_LINE uses 1-based line numbers (aux1 must be > 0). For lists, it uses 0-based indexing; with start_from_end, negative indexing is applied.
- **REPLACE count**: If aux3 is 0 or not provided, replacement is treated as unlimited (i or count becomes -1).
- **SPLIT max splits**: For SPLIT, aux2 is the max splits; 0 means unlimited.
- **JOIN behavior**: JOIN uses aux1 as the separator to join elements of the input list into a string. It fails (result False) if input isn't iterable of strings or coercible.
- **RANDOM_ELEMENT pop**: If aux1 is truthy, RANDOM_ELEMENT removes (pop) and returns a random element; otherwise it returns a random element without modifying the list.
- **TO_LIST conversion**: Converts a string into a list of characters; a list input is passed through.

## Troubleshooting
- **Unexpected False result**: Ensure input types match the operation (e.g., SPLIT on strings only; RANDOM_ELEMENT on lists).
- **Index errors in GET_LINE**: For strings, provide a positive line number (starting at 1). For lists, ensure the index is within range; consider disabling start_from_end if indexing becomes negative unexpectedly.
- **No changes from REPLACE**: Verify aux1 (pattern) and aux2 (replacement) are set. For case-insensitive replacements, set case_insensitive to true. If no matches, output remains unchanged and result is True.
- **FIND returns -1**: The substring wasn’t found. Check case_insensitive and aux2/aux3 (start and end positions) if used. When applied to lists, inspect the per-element results.
- **JOIN fails**: Make sure input is a list of strings or objects that can be converted to strings. Provide a separator in aux1 (default is empty string if not provided).
- **SPLIT/SPLIT_LINES errors**: These operations do not accept list inputs. Convert to a single string first (e.g., JOIN) or use TO_STRING where appropriate.
- **IS_NUMERIC is False**: The value might contain non-numeric characters or be a list. Provide a plain numeric string or number-like input.
- **Unexpected reversal of text**: If output appears reversed, ensure start_from_end is set correctly. Many operations reset it internally, but some outcomes can still reflect reversed processing.
