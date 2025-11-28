# String Condition

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates boolean conditions between two strings. Supports equality, inequality, substring checks, regex match, and prefix/suffix checks, with optional case sensitivity. Returns a single boolean result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltstringconditions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to gate logic based on string comparisons, such as validating user input, filtering text, or controlling flow based on names, tags, or identifiers. Select the desired operation, provide the two strings, and set case sensitivity to match your intended behavior.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Left-hand string value to compare.</td><td style="word-wrap: break-word;">Hello World</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Right-hand string value used as comparison target or pattern.</td><td style="word-wrap: break-word;">hello</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Comparison to perform between a and b. Options: a == b, a != b, a IN b (substring), a MATCH REGEX(b) (pattern match), a BEGINSWITH b (prefix), a ENDSWITH b (suffix).</td><td style="word-wrap: break-word;">a IN b</td></tr>
<tr><td style="word-wrap: break-word;">case_sensitive</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If false, both a and b are lowercased before comparison (including regex pattern).</td><td style="word-wrap: break-word;">false</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True if the selected condition holds for the given inputs; otherwise False.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Case sensitivity**: When case_sensitive is false, both a and b are converted to lowercase before evaluating the condition (including regex patterns).
- **Regex match**: a MATCH REGEX(b) uses a pattern match against the start of a (equivalent to a 'match', not a 'search'). Invalid regex patterns produce False.
- **Substring semantics**: a IN b returns True if a occurs anywhere within b.
- **Prefix/Suffix**: a BEGINSWITH b checks a starts with b; a ENDSWITH b checks a ends with b.
- **Empty strings**: Be mindful that empty strings can lead to True for certain operations (e.g., '' IN b, or any string begins/ends with '').

## Troubleshooting
- **Unexpected False with regex**: Ensure b is a valid regex. If case_sensitive is false, remember your pattern is lowercased; escape special characters if you intend a literal match.
- **Case-related mismatches**: Toggle case_sensitive or normalize your inputs to achieve the expected comparison.
- **Substring direction confusion**: For 'a IN b', the left value a is the substring and the right value b is the container; reverse them if you intended the opposite.
- **Leading/trailing spaces**: Trim whitespace in a and b if spacing causes unintended results.
- **Prefix/Suffix confusion**: For 'a BEGINSWITH b' and 'a ENDSWITH b', b is the prefix/suffix to check against a.
