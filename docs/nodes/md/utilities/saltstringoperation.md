# String Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node exposes a wide range of string and list operations behind a single interface controlled by an operation selector. It can transform values (case, slice, reverse, split/join, generate, replace), perform checks (is numeric/alpha, starts/ends with patterns), and support random selection from inputs. It outputs both a transformed value and an auxiliary result (such as length, indices, or boolean) suitable for downstream logic.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltstringoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to manipulate or analyze text or lists in flexible ways without adding many separate utility nodes. For example, you can clean and normalize messages (TRIM_SPACES, STRIP, LOWERCASE/UPPERCASE/PROPERCASE), extract or inspect content (FIND, GET_LINE, SPLIT_LINES, SPLIT, LENGTH, COUNT), build complex prompts or messages (CONCATENATE, JOIN, GENERATE, REPLACE), validate values (IS_NUMERIC, IS_ALPHA, STARTS_WITH, ENDS_WITH), or introduce stochastic behavior (RANDOM_INPUT, RANDOM_ELEMENT). Place it after sources that produce raw text or lists and before nodes that depend on clean, structured content or on indices and boolean flags, such as conditional routing, template construction, or storage. Typical patterns include: taking a multi-line API response and using SPLIT_LINES followed by GET_LINE to extract a specific section; using REPLACE and TRIM_SPACES to clean user-entered text; converting a list of tags into a comma-separated string via JOIN; slicing an input string with SLICE to keep just a prefix; or picking a random variant of a message using RANDOM_INPUT across `input` and aux inputs. Use `start_from_end` when you want operations like FIND, STARTS_WITH/ENDS_WITH, or GET_LINE to conceptually work from the end of the string or list, and `case_insensitive` when searching or replacing text without regard to letter case. Configure operation-specific parameters via aux1–aux3 or param1–param3, remembering that non-empty param values override their corresponding aux inputs.

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
<tr><td style="word-wrap: break-word;">input</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The primary value to process. It can be a string or a list, depending on the chosen operation. Many operations treat it as a string (LENGTH, COUNT, FIND, SPLIT, SPLIT_LINES, REPLACE, UPPERCASE, LOWERCASE, PROPERCASE, STRIP, TRIM_SPACES), while others are designed for lists (RANDOM_ELEMENT, JOIN, TO_LIST, GENERATE when starting from a list). The node attempts to convert non-list inputs to strings where necessary.</td><td style="word-wrap: break-word;">"User: Alice\nEmail: alice@example.com\nStatus: Active" or ["Alice", "Bob", "Charlie"]</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">Specifies which operation to execute. Supported options are: "COMPARE", "CONCATENATE", "COUNT", "ENDS_WITH", "EXTRACT_BETWEEN", "FIND", "GENERATE", "GET_LINE", "IS_ALPHA", "IS_NUMERIC", "JOIN", "LENGTH", "LOWERCASE", "PROPERCASE", "REPLACE", "RANDOM_INPUT", "RANDOM_ELEMENT", "REVERSE", "SLICE", "SPLIT", "SPLIT_LINES", "STARTS_WITH", "STRIP", "TO_LIST", "TO_STRING", "TRIM_SPACES", "UPPERCASE". Not all operations require all aux or param inputs.</td><td style="word-wrap: break-word;">REPLACE</td></tr>
<tr><td style="word-wrap: break-word;">start_from_end</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls directional behavior for operations that are position-sensitive. When true, the node often reverses the input internally before applying operations such as STARTS_WITH, ENDS_WITH, FIND, SPLIT, SPLIT_LINES, GET_LINE, IS_NUMERIC, and IS_ALPHA, then may reverse results back at the end. This can change which characters or list elements are considered the start or end of the expression.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">case_insensitive</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Enables case-insensitive behavior for supported operations (STARTS_WITH, ENDS_WITH, FIND, REPLACE, and STRIP/TRIM_SPACES when using aux1). When true, comparisons and replacements will typically lowercase both the input and the pattern so different letter cases are treated equivalently. Has no effect on inherently case-neutral operations like LENGTH.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">aux1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First auxiliary input. Its meaning depends on the chosen operation. Common patterns: for REPLACE, the substring or pattern to replace; for FIND, the substring to search for; for STARTS_WITH and ENDS_WITH, the prefix or suffix; for GENERATE, the number of repetitions; for GET_LINE, the line index (1-based for strings, direct index for lists); for JOIN, the separator; for RANDOM_ELEMENT, a flag controlling whether to remove the selected element from the list. If `param1` is provided and not empty, it overrides aux1.</td><td style="word-wrap: break-word;">For REPLACE: "error"; for FIND: "Status:"; for GENERATE: 3; for GET_LINE: 2; for JOIN: ", "</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second auxiliary input used where operations need an additional parameter. Typical uses: for REPLACE, the replacement value; for GENERATE, the separator inserted between repetitions; for FIND, the numeric start index; for SLICE, the stop index; for SPLIT, the maximum number of splits (0 or None means unlimited). If `param2` is non-empty, it overrides aux2.</td><td style="word-wrap: break-word;">For REPLACE: "issue"; for GENERATE: "\n"; for FIND: 0; for SLICE: 25; for SPLIT: 3</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third auxiliary input, typically used for extra numeric parameters. Common roles: for REPLACE, maximum number of replacements (0 or None for unlimited); for FIND, an end index; for SLICE, the step value used in slicing. If `param3` is non-empty, it overrides aux3.</td><td style="word-wrap: break-word;">For REPLACE: 1 (replace only the first match); for SLICE: 2 (take every second character); for FIND: 50 (search until position 50)</td></tr>
<tr><td style="word-wrap: break-word;">param1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">String override for aux1. When non-empty, this value is used instead of aux1. It is ideal for configuring constant values directly in the UI, such as a fixed prefix, suffix, or search term, while still allowing dynamic values via aux1 when left empty.</td><td style="word-wrap: break-word;">For STARTS_WITH: "Status:"</td></tr>
<tr><td style="word-wrap: break-word;">param2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">String override for aux2. When this field is not empty, its value replaces aux2 for the operation. Often used to specify a literal replacement string, a join separator, or a repetition separator without needing an upstream node.</td><td style="word-wrap: break-word;">For JOIN: "; "; for REPLACE: "warning"</td></tr>
<tr><td style="word-wrap: break-word;">param3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">String override for aux3. If non-empty, this value is used instead of aux3 and will typically be interpreted as a numeric string when relevant, for example replacement count, index, or step.</td><td style="word-wrap: break-word;">For REPLACE: "0" to indicate unlimited replacements; for SLICE: "1"</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The transformed primary value after applying the selected operation. Depending on operation and input type, this can be a string (for example after REPLACE, UPPERCASE, LOWERCASE, PROPERCASE, STRIP, TRIM_SPACES), a list (for example after SPLIT, SPLIT_LINES, GENERATE, TO_LIST, REVERSE on lists), or a single element from a list (for example RANDOM_ELEMENT when returning or popping one item).</td><td style="word-wrap: break-word;">For REPLACE on input="Order error: 404" with aux1="error" and aux2="issue" -> "Order issue: 404"; for SPLIT_LINES on a 3-line log string -> ["INFO Start", "WARN Disk low", "INFO Done"]</td></tr>
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Secondary result summarizing the operation outcome. It is boolean for checks like IS_NUMERIC, IS_ALPHA, STARTS_WITH, and ENDS_WITH; an integer for LENGTH, SLICE, SPLIT, SPLIT_LINES, STRIP, and TRIM_SPACES (often the resulting length or count); an index or list of indices for FIND (or -1 if not found); an index for RANDOM_INPUT and RANDOM_ELEMENT; and typically true or false for transformation operations like REPLACE, GENERATE, JOIN, TO_LIST, TO_STRING, UPPERCASE, LOWERCASE, and PROPERCASE.</td><td style="word-wrap: break-word;">For LENGTH on ["a","b","c"] -> 3; for FIND on "User: Alice" with aux1="Alice" -> 6; for IS_NUMERIC on "12.5" -> true; for RANDOM_ELEMENT on a 5-item list -> an integer from 0 to 4</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Operations are generally linear in the size of the input string or list, but combinations like GENERATE with a high repetition count or SPLIT/SPLIT_LINES on very long texts can allocate large intermediate structures; avoid extremely large values in tight loops.
- **Limitations**: Some operations have strict type expectations: RANDOM_ELEMENT requires `input` to be a list, while SPLIT and SPLIT_LINES require `input` to be a string and will raise an exception if given a list.
- **Behavior**: When `start_from_end` is true, the node often reverses `input` internally, performs the operation, then may reverse `output` back; this changes which element is treated as the beginning for operations like GET_LINE, STARTS_WITH, ENDS_WITH, and FIND and can subtly affect results when using indices.
- **Behavior**: `param1`, `param2`, and `param3` always override aux1, aux2, and aux3 when they are non-empty, which can lead to unexpected behavior if you wire both; make sure to either leave the param fields empty when driving via aux inputs or rely solely on params for constants.
- **Behavior**: On exceptions inside the operation logic (for example wrong types or invalid indices), the node logs an error and sets `result` to false, but still returns an `output` value (possibly unchanged or partially transformed), so downstream logic should always inspect `result` before trusting `output`.

## Troubleshooting
- **Common Error 1**: Message "Input cannot be successfully converted to a string!" – This indicates that `input` is of a type that could not be safely coerced to text. Ensure the upstream node outputs strings, numbers, or lists of these, or pre-process complex objects into textual form before using this node.
- **Common Error 2**: Exception stating "The RANDOM_ELEMENT operation expects a List for Input!" – You used RANDOM_ELEMENT with a non-list `input`. Wrap individual items into a list upstream, or use RANDOM_INPUT if you want to randomly choose among up to four separate inputs (input, aux1, aux2, aux3).
- **Common Error 3**: Exception like "Aux1 must be a positive number for the GENERATE operation!" or similar for GET_LINE – The node expects aux1 (or param1) to be a positive integer. Double-check that you are passing a strictly positive whole number, not zero, negative values, or non-numeric strings.
- **Common Error 4**: SPLIT or SPLIT_LINES failing when `input` is a list – These operations are defined only for string inputs and will raise an error if a list is passed. Convert your list into a string (for example via JOIN) before splitting, or ensure the source node provides string data.
- **Common Error 5**: REPLACE or FIND seems to ignore matches – If casing differs between the target substring and the content, and `case_insensitive` is false, matches will not be found. Either enable `case_insensitive` or normalize both the input and pattern (for example lowercase them) before using these operations.
