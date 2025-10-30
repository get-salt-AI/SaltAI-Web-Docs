# Regex Search and Replace

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs a regular-expression-based search and replace on the provided text. It replaces all occurrences that match the given regex pattern with the specified replacement text. Supports multiline input and backreferences in the replacement.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/Utilities/SAIStringRegexSearchReplace.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to systematically modify text using patterns—such as masking sensitive data, normalizing formats, or transforming structured strings. Place it in workflows that clean, preprocess, or reformat text before downstream processing or storage.

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
<tr><td style="word-wrap: break-word;">text_input</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The source text where regex matches will be searched and replaced.</td><td style="word-wrap: break-word;">The quick brown fox jumps over 123 lazy dogs.</td></tr>
<tr><td style="word-wrap: break-word;">regex_pattern</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A Python-compatible regular expression pattern used to find text to replace.</td><td style="word-wrap: break-word;">\b\w{5}\b</td></tr>
<tr><td style="word-wrap: break-word;">replacement_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Text to substitute for each match found by the regex. Supports backreferences like \1, \2 for captured groups.</td><td style="word-wrap: break-word;">[REDACTED]</td></tr>
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
<tr><td style="word-wrap: break-word;">replaced_text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The resulting text after applying the regex-based replacements to the input.</td><td style="word-wrap: break-word;">The [REDACTED] brown [REDACTED] jumps over 123 lazy dogs.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Regex engine**: Uses Python regular expressions. Ensure your pattern is valid for Python’s regex syntax.
- **Global replacement**: All matches in the text are replaced (no limit parameter).
- **Backreferences**: You can reference captured groups in the replacement text (e.g., \1).
- **Flags**: There is no separate flags input. Use inline flags in the pattern if needed (e.g., (?i) for case-insensitive).
- **Escaping**: When entering patterns with backslashes, ensure proper escaping in the UI (e.g., \\b for a word boundary).

## Troubleshooting
- **Invalid regex pattern (re.error)**: If the node errors, verify the pattern is valid and properly escaped.
- **No changes in output**: Confirm the pattern actually matches the input. Test with simpler patterns or use a match node to verify.
- **Unexpected replacements**: Anchor your pattern (e.g., \b, ^, $) or make it more specific to avoid over-matching.
- **Backreference not working**: Ensure you used capturing groups in the pattern, and reference them correctly in the replacement (e.g., (pattern) with \1).
- **Multiline behavior**: If matches don’t cross lines as expected, add inline flags like (?s) for DOTALL or (?m) for multiline.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/Utilities/SAIStringRegexSearchReplace/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

