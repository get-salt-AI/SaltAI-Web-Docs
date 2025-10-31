# Regex Search and Match

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches the input text using a regular expression and returns all matches. It uses Python’s regex engine to find non-overlapping matches in order, producing a list of results.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/SAIStringRegexSearchMatch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to extract all substrings that match a pattern, such as extracting emails, hashtags, words of a certain length, or other structured tokens from text. Typically placed after a text generation or text input node to parse and collect specific elements.

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
<tr><td style="word-wrap: break-word;">text_input</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text to search for matches using the provided regular expression.</td><td style="word-wrap: break-word;">The quick brown fox jumps over 2 lazy dogs.</td></tr>
<tr><td style="word-wrap: break-word;">regex_pattern</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Regular expression pattern used to find matches. Supports Python regex syntax, including inline flags like (?i) for case-insensitive matching.</td><td style="word-wrap: break-word;">\b\w{5}\b</td></tr>
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
<tr><td style="word-wrap: break-word;">matches</td><td style="word-wrap: break-word;">LIST</td><td style="word-wrap: break-word;">List of all non-overlapping matches found in the input text. If the pattern contains capture groups, each match may be a tuple of group values; otherwise, each match is a string.</td><td style="word-wrap: break-word;">['quick', 'brown']</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The matching behavior follows Python's re.findall: results are strings if there are no capture groups, or tuples if the pattern includes groups.
- If no matches are found, the output is an empty list.
- Regex flags are not separate inputs; use inline flags such as (?i) for case-insensitive or (?s) for dotall within the pattern.
- Be mindful of escaping backslashes in patterns (e.g., use \\b for word boundary in string literals).
- Very large texts or complex patterns can impact performance; simplify patterns or pre-filter text when possible.

## Troubleshooting
- Pattern error (e.g., 'bad character range'): Ensure the regex is valid Python syntax and properly escaped.
- Unexpected tuples in output: Remove capture groups or convert them to non-capturing groups using (?:...) to get full-match strings.
- Case sensitivity issues: Add (?i) at the start of the pattern for case-insensitive matching.
- No matches returned: Verify the pattern, test with simpler patterns, or check that the input text actually contains the target substrings.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/SAIStringRegexSearchMatch/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

