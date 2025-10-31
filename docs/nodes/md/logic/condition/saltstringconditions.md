# String Condition

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates conditions between two strings and returns a boolean result. Supports equality/inequality, substring containment, regex match, and prefix/suffix checks. Case sensitivity can be toggled; when disabled, both inputs are lowercased before evaluation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltstringconditions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to build conditional logic that depends on string comparisons, such as filtering text, routing workflow branches, or validating inputs. Select an operation, provide the two strings, and optionally disable case sensitivity. The node outputs a single boolean you can feed into downstream logic or branching nodes.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Left-hand string operand for the comparison.</td><td style="word-wrap: break-word;">Hello World</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Right-hand string operand. For regex, this is the pattern applied to 'a'. For 'IN', 'BEGINSWITH', and 'ENDSWITH', this is the target or substring to compare against.</td><td style="word-wrap: break-word;">hello\|hi\|hey</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The comparison to perform. Options: 'a == b', 'a != b', 'a IN b' (substring containment), 'a MATCH REGEX(b)' (regex applied to 'a'), 'a BEGINSWITH b', 'a ENDSWITH b'.</td><td style="word-wrap: break-word;">a MATCH REGEX(b)</td></tr>
<tr><td style="word-wrap: break-word;">case_sensitive</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If false, both 'a' and 'b' are lowercased before comparison (affects regex patterns as well).</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean outcome of the selected string condition.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Case sensitivity**: When disabled, the node lowercases both 'a' and 'b' before evaluation. This impacts regex patterns as they are also lowercased.
- **Regex behavior**: Uses a regex match that tests from the start of the string (start-anchored behavior). Provide patterns accordingly (e.g., use ".*" if you intend to match anywhere).
- **Invalid regex**: If 'b' is not a valid regex pattern, the result is False.
- **Unknown operation**: If an unsupported operation is somehow provided, the result is False.
- **Error handling**: On internal errors, the node returns False.
- **Operation meanings**: 'a IN b' checks whether 'a' is a substring of 'b'; 'a BEGINSWITH b' means 'a' starts with 'b'; 'a ENDSWITH b' means 'a' ends with 'b'.

## Troubleshooting
- **Unexpected False with regex**: Ensure 'b' is a valid regex and remember the match is from the start of 'a'. If you need to match anywhere, adjust your pattern (e.g., use ".*pattern" or anchors).
- **Case-insensitive regex not working as expected**: With case_sensitive disabled, both text and pattern are lowercased. Avoid character classes or ranges that rely on original casing; adapt the pattern for lowercase.
- **Substring checks reversed**: For 'a IN b', remember it checks whether 'a' appears inside 'b'. Swap inputs if you intended the opposite.
- **Accents or locale cases**: Lowercasing is used (not full case folding). If special locale handling is needed, ensure inputs are normalized beforehand.
- **Always False result**: Verify the chosen operation string exactly matches one of the supported options and that both inputs are provided.
