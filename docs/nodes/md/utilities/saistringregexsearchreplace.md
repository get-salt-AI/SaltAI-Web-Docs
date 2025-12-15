# Regex Search and Replace

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs a regular-expression-based search and replace on the provided text. It replaces all occurrences that match the pattern with the specified replacement text using Python's regex syntax. Useful for cleaning, transforming, or masking parts of text.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saistringregexsearchreplace.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to transform text by pattern—e.g., redact emails, normalize formatting, or substitute tokens. Provide the input text, a valid regex pattern, and the replacement string; the node outputs the fully replaced text. Integrate it before downstream nodes that require cleaned or standardized inputs.

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
<tr><td style="word-wrap: break-word;">text_input</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The source text in which to search for matches and perform replacements.</td><td style="word-wrap: break-word;">Contact me at john.doe@example.com or jane@example.org.</td></tr>
<tr><td style="word-wrap: break-word;">regex_pattern</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The regular expression to match. Uses Python regex syntax; inline flags like (?i) for case-insensitive are supported.</td><td style="word-wrap: break-word;">\b[\w.%+-]+@[\w.-]+\.[A-Za-z]{2,}\b</td></tr>
<tr><td style="word-wrap: break-word;">replacement_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Text to replace each match. Supports backreferences such as \1, \2 corresponding to capturing groups in the pattern.</td><td style="word-wrap: break-word;"><redacted-email></td></tr>
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
<tr><td style="word-wrap: break-word;">replaced_text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The resulting text after applying the regex replacement to all matches.</td><td style="word-wrap: break-word;">Contact me at <redacted-email> or <redacted-email>.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Pattern syntax**: Follows Python regular expression rules; ensure proper escaping of backslashes in patterns.
- **Global replacement**: All matches in the input are replaced (not just the first).
- **Backreferences**: Use \1, \2, etc., in the replacement to insert captured groups.
- **Inline flags**: Use inline modifiers (e.g., (?i) case-insensitive, (?m) multiline) within the pattern if needed; there is no separate flags input.
- **Escaping in UI/JSON**: Remember to double-escape backslashes (e.g., \\d) when entering patterns in JSON-like fields.

## Troubleshooting
- **Invalid pattern error**: If execution fails, verify the regex is valid and properly escaped (e.g., use \\b instead of \b in JSON strings).
- **Unexpected replacements**: Add anchors (^, $), word boundaries (\\b), or use non-greedy quantifiers (e.g., .*?) to constrain matches.
- **Case sensitivity issues**: Add (?i) at the start of your pattern for case-insensitive matching.
- **Group references not working**: Ensure your pattern includes capturing groups (parentheses) and reference them correctly in the replacement (e.g., \1).
- **Performance on large text**: Complex patterns on very large strings may be slow; simplify the regex or narrow the scope where possible.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/saistringregexsearchreplace/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

