# Regex Search and Match

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Searches an input string using a regular expression and returns all matches as a list. It supports multiline text input and standard regular expression syntax. Results reflect Python-style regex behavior, including how capture groups affect output.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saistringregexsearchmatch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to extract patterns from text, such as emails, IDs, words of a certain length, or any custom pattern. Typically placed after a node that provides text content and before downstream nodes that iterate over or analyze the extracted matches.

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
<tr><td style="word-wrap: break-word;">text_input</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The text to search for matches. Supports multiline input.</td><td style="word-wrap: break-word;">Please contact us at support@example.com or sales@example.org.</td></tr>
<tr><td style="word-wrap: break-word;">regex_pattern</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Regular expression pattern to find matches. Uses Python regex syntax. You can use inline flags like (?i) for case-insensitive matches.</td><td style="word-wrap: break-word;">\b[a-zA-Z]{6}\b</td></tr>
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
<tr><td style="word-wrap: break-word;">matches</td><td style="word-wrap: break-word;">LIST</td><td style="word-wrap: break-word;">List of all matches found in the input text. If the pattern contains capture groups, each match may be a string (no groups) or a tuple (one or more groups). Returns an empty list if no matches are found.</td><td style="word-wrap: break-word;">['support', 'sales']</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Regex behavior**: Matches are computed with Python's re.findall. If your pattern contains capture groups, the output elements may be tuples rather than plain strings.
- **No explicit flags input**: Inline flags like (?i), (?m), (?s) can be embedded in the pattern to control case sensitivity, multiline, and dotall behavior.
- **Escaping**: Remember to escape backslashes in patterns (e.g., use "\\b" for a word boundary).
- **Empty results**: If no matches are found, the node outputs an empty list.
- **Invalid patterns**: A malformed regex pattern will cause an error; validate your pattern before running.

## Troubleshooting
- **Unexpected empty list**: Verify the pattern matches your text and consider case sensitivity. Add inline flags like (?i) if needed.
- **Matches are tuples instead of strings**: Your pattern contains capture groups. Use non-capturing groups (?:...) or adjust downstream logic.
- **Regex compilation error**: The pattern is invalid. Test and correct the pattern (e.g., unbalanced brackets or parentheses).
- **Greedy vs. non-greedy matches**: If matches are larger than expected, use non-greedy quantifiers like *? or +?.
- **Escaping issues**: Double-escape backslashes in the pattern string (e.g., "\\d+" for digits).

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/saistringregexsearchmatch/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

