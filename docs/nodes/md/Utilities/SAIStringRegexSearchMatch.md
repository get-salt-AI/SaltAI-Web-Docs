# Regex Search and Match

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches the provided text with a regular expression and returns all matches. Uses Python-style regex semantics and returns a list of matches based on the pattern.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/Utilities/SAIStringRegexSearchMatch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to extract all occurrences of patterns from text, such as emails, specific word lengths, tokens, or structured substrings. Connect a text source to text_input, provide a valid regex_pattern, and consume the resulting list of matches in downstream logic.

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
<tr><td style="word-wrap: break-word;">text_input</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text to search for matches. Supports multi-line content.</td><td style="word-wrap: break-word;">Contact us at support@example.com or sales@example.org.</td></tr>
<tr><td style="word-wrap: break-word;">regex_pattern</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A Python-compatible regular expression used to find matches. Inline flags like (?i) are supported.</td><td style="word-wrap: break-word;">\b[a-zA-Z]{6}\b</td></tr>
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
<tr><td style="word-wrap: break-word;">matches</td><td style="word-wrap: break-word;">LIST</td><td style="word-wrap: break-word;">A list of all matches found in the input text. If the pattern contains capture groups, each item may be a string (single group) or a tuple (multiple groups).</td><td style="word-wrap: break-word;">['support', 'sales']</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Capture groups affect results**: If your regex includes groups, each match will be the captured group(s) rather than the full match; with multiple groups, each item is a tuple.
- **No global flags parameter**: Provide flags inline in the pattern (e.g., (?i) for case-insensitive, (?m) for multiline, (?s) for dotall).
- **Non-overlapping matches**: Matches are found in a non-overlapping manner, following Python's re.findall behavior.
- **Empty results**: If no matches are found, the output is an empty list.
- **Escaping**: Remember to escape backslashes in patterns (e.g., use \\b for a word boundary).

## Troubleshooting
- **Invalid regex error**: If the node errors, verify the regex syntax and escape sequences. Test the pattern in a Python regex tester.
- **Unexpected tuples in output**: Remove capture groups or convert them to non-capturing groups (?:...) if you want full-match strings instead of tuples.
- **Case sensitivity issues**: Add (?i) to the pattern for case-insensitive matching.
- **Multiline text not matching**: Add (?m) or (?s) as needed to handle line boundaries or dot matches newline.
- **Performance on large text**: Simplify the pattern or narrow the search scope to avoid heavy processing on very large inputs.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/Utilities/SAIStringRegexSearchMatch/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

