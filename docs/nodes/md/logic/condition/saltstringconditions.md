# String Condition

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates a boolean condition between two strings using common string operations. Supports equality/inequality, containment, regex match, and prefix/suffix checks, with an option to enable or disable case sensitivity. Returns a single boolean result indicating whether the condition holds.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltstringconditions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to branch logic or filter data based on string relationships. Typical scenarios include checking if a keyword exists in text, ensuring exact matches, validating formats via regex, or confirming prefixes/suffixes. Configure case sensitivity to control whether comparisons normalize case before evaluation.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">First string to evaluate (left-hand side in the selected operation).</td><td style="word-wrap: break-word;">hello</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Second string to evaluate (right-hand side in the selected operation). Used as the comparator, container, pattern, prefix, or suffix depending on the operation.</td><td style="word-wrap: break-word;">Hello world</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The string operation to evaluate: one of 'a == b', 'a != b', 'a IN b', 'a MATCH REGEX(b)', 'a BEGINSWITH b', or 'a ENDSWITH b'.</td><td style="word-wrap: break-word;">a IN b</td></tr>
<tr><td style="word-wrap: break-word;">case_sensitive</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, comparisons are case-sensitive. If false, both inputs are transformed to lowercase before evaluation (including the regex pattern).</td><td style="word-wrap: break-word;">False</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean outcome of the selected string condition.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Case sensitivity applies to all supported operations. When disabled, both inputs (including the regex pattern) are lowercased before evaluation.
- Regex is evaluated using a 'match' operation, which tests from the start of the string rather than anywhere in the string. Use patterns accordingly (e.g., '.*' to match anywhere).
- If the provided regex pattern is invalid, the node returns False and logs a warning.
- 'a IN b' checks whether the entire string 'a' is a substring of 'b'.
- Unknown operations result in a False outcome.
- Inputs are single-line strings; trim or normalize your inputs if leading/trailing whitespace matters.

## Troubleshooting
- Unexpected False with regex: Ensure your pattern is valid and consider that matching starts at the beginning of the string. Add '.*' or anchors as needed.
- Case-related mismatches: Toggle 'case_sensitive' or normalize your inputs if expected matches fail due to casing.
- Containment not working: Verify that 'a' is the substring you expect to find within 'b' (the operation is 'a IN b', not 'b IN a').
- Prefix/suffix checks failing: Confirm that whitespace or hidden characters are not affecting startswith/endswith results.
- Always False: Confirm the 'operation' selection is one of the supported options and that both inputs are provided.
