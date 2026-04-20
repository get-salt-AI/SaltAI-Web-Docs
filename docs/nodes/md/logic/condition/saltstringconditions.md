# String Condition

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node compares two strings using a selected operation and outputs a boolean value. It supports equality, inequality, substring containment, regular expression matching, and prefix/suffix checks, with an option to ignore case. Invalid regex patterns or unknown operations are handled safely by returning false instead of failing the workflow.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltstringconditions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to make decisions in a Salt workflow based on how two strings relate to each other. Common scenarios include checking if a user message contains a keyword, verifying that a value matches a pattern (such as a simple ID or code format), or ensuring a string starts or ends with a specific marker (such as prefixes in commands or suffixes in filenames). Place it after nodes that produce or extract text, such as HTTP response parsers, variable mappers, or text transformation nodes, and before branching or logic nodes like IfConditionSelector, SaltBoolOperationNode, or SaltToBoolNode. Combine it with SaltIntConditions and SaltFloatConditions to build composite business rules across multiple data types. Prefer simple operations (equality, IN, BEGINSWITH, ENDSWITH) for clarity and performance, and only use regex when you truly need pattern matching. Use the case_sensitive flag to control how strictly you match user inputs or external data, especially in multilingual or user-generated content scenarios.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The primary string to evaluate in the condition. It is the left-hand side operand and is compared against b according to the chosen operation. Intended for single-line text; very large strings may slow down operations, especially regex matching.</td><td style="word-wrap: break-word;">"Order status: PENDING"</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The comparison string or pattern, interpreted according to the selected operation. For equality and inequality it is the target value; for a IN b it is the container string; for a MATCH REGEX(b) it must be a valid regex pattern; for BEGINSWITH and ENDSWITH it is the prefix or suffix to check.</td><td style="word-wrap: break-word;">"PENDING"</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Specifies which string comparison to perform. Allowed values are: "a == b" (exact equality), "a != b" (inequality), "a IN b" (true if a is a substring of b), "a MATCH REGEX(b)" (true if a matches regex pattern b), "a BEGINSWITH b" (true if a starts with b), and "a ENDSWITH b" (true if a ends with b). Any other value is treated as unknown and yields false.</td><td style="word-wrap: break-word;">"a IN b"</td></tr>
<tr><td style="word-wrap: break-word;">case_sensitive</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls whether comparisons are case sensitive. If false, both a and b are converted to lowercase before evaluation for all operations, including regex. This can simplify matching user input but may change the behavior of some regex patterns. Default is true.</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean outcome of the selected string condition. It is true if the chosen operation holds between a and b after optional case normalization, otherwise false. Invalid regex patterns, unknown operations, or unexpected internal errors all result in false so that downstream logic can safely treat the condition as not met.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Regex checks with the a MATCH REGEX(b) operation are more expensive than simple equality or substring checks, and may noticeably slow workflows when used on very long strings or in tight loops.
- **Limitations**: For the a IN b operation, the node checks whether a is contained inside b; if you conceptually think in the opposite direction, you must swap operands before passing them to this node.
- **Behavior**: When case_sensitive is false, both strings are lowercased before comparison, which also affects how regex patterns behave; any uppercase-specific constructs in the pattern will effectively be lost.
- **Behavior**: Invalid regex patterns and unknown operations do not raise user-facing errors; they are logged internally and the node simply returns false, which may look like a normal failed condition in your logic.

## Troubleshooting
- **Unexpected false with regex**: If result is always false for a MATCH REGEX(b), first verify that b is a valid regex pattern and that it matches the full or partial content of a as you expect; also check if case_sensitive is false, since lowercasing can change the intended pattern behavior.
- **IN operation appears reversed**: If a IN b returns false when you expect true, confirm that a is the substring and b is the longer text; accidentally swapping them is a common source of confusion.
- **Case-related mismatches**: If comparisons fail only when the casing differs, set case_sensitive to false to normalize both sides to lowercase; if you need strict casing, ensure that case_sensitive is true and that upstream transformations have not altered text case.
- **Always false result**: If result is consistently false regardless of input, check that operation is exactly one of the supported strings ("a == b", "a != b", "a IN b", "a MATCH REGEX(b)", "a BEGINSWITH b", "a ENDSWITH b") with no typos, extra spaces, or variant formatting.
