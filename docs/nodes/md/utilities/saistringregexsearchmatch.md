# Regex Search and Match

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches an input string using a regular expression and returns all matches. It performs a global search across the entire text and outputs the results as a list. If the pattern contains capture groups, the output reflects those groups.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saistringregexsearchmatch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to extract all occurrences that match a specific pattern from a block of text (e.g., emails, IDs, keywords of a certain length). Typically used before downstream parsing, validation, filtering, or branching logic that depends on detected tokens.

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
<tr><td style="word-wrap: break-word;">text_input</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text to search. Can be multi-line.</td><td style="word-wrap: break-word;">Please contact alice@example.com or bob@example.org for details.</td></tr>
<tr><td style="word-wrap: break-word;">regex_pattern</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Regular expression to match against the input text. Use standard regex syntax. Backslashes must be escaped in string literals.</td><td style="word-wrap: break-word;">\b[a-zA-Z]{6}\b</td></tr>
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
<tr><td style="word-wrap: break-word;">matches</td><td style="word-wrap: break-word;">LIST</td><td style="word-wrap: break-word;">A list of all matches found. If the pattern has capture groups, each match may be a string (no groups) or a tuple/list (with groups). Returns an empty list if no matches are found.</td><td style="word-wrap: break-word;">['alice', 'bobcat']</td></tr>
</tbody>
</table>
</div>

## Important Notes
- If the regex pattern contains capture groups, each item in the output may be a tuple of captured group values rather than a single string.
- Invalid regex patterns will cause an error; verify your pattern syntax.
- The search is global across the entire input and is not case-insensitive unless specified in the pattern (e.g., (?i)).
- Remember to double-escape backslashes in patterns entered as strings (e.g., "\\d+" for digits).
- Performance can degrade with very large texts or complex patterns; optimize your regex when processing long documents.
- By default, dot (.) does not match newlines unless you use modifiers like (?s).

## Troubleshooting
- Pattern returns an empty list: Confirm the text truly contains matches and that anchors/boundaries (e.g., \b, ^, $) are appropriate for your data.
- Unexpected tuple outputs: Remove capture groups or convert them to non-capturing groups (?:...) if you want full-match strings.
- Regex syntax error: Validate the pattern with a regex tester or simplify the expression to locate the error.
- Missing matches due to case: Add (?i) to the pattern or explicitly include case ranges.
- Multiline behavior issues: Use (?m) for ^/$ across lines or (?s) for dotall behavior if needed.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/saistringregexsearchmatch/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

