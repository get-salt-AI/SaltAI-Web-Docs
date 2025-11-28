# Regex Search and Replace

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Finds all matches of a regular expression in the input text and replaces them with the provided replacement text. Uses standard regular expression behavior, supporting boundaries, character classes, groups, and backreferences in the replacement. Designed for straightforward, single-pass replacements over an entire string.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saistringregexsearchreplace.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to normalize, redact, or transform text based on patterns (e.g., replace all 5-letter words, mask emails, reformat dates). Place it after any node that produces text and before downstream nodes that consume the transformed result.

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
<tr><td style="word-wrap: break-word;">text_input</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The source text in which the regex will search for matches to replace.</td><td style="word-wrap: break-word;">Contact me at john.doe@example.com or jane@example.org.</td></tr>
<tr><td style="word-wrap: break-word;">regex_pattern</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Regular expression pattern to match. Follows Python-style regex syntax, including groups and anchors.</td><td style="word-wrap: break-word;">\b\w{5}\b</td></tr>
<tr><td style="word-wrap: break-word;">replacement_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Text used to replace each match. Can include backreferences like \1 for captured groups.</td><td style="word-wrap: break-word;">[REDACTED]</td></tr>
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
<tr><td style="word-wrap: break-word;">replaced_text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The resulting text after all matches of the pattern have been replaced.</td><td style="word-wrap: break-word;">Contact me at [REDACTED] or [REDACTED].</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Regex flavor**: Uses Python-style regular expressions (e.g., groups ( ), non-greedy quantifiers ?, word boundaries \b).
- **Escaping**: When entering patterns in a string field, backslashes must be escaped (e.g., use \\b for a word boundary).
- **Flags**: There is no separate flags input; use inline flags in the pattern if needed (e.g., (?i) for case-insensitive, (?s) to make . match newlines).
- **Backreferences**: Use numeric backreferences like \1, \2 in the replacement_text to reuse captured groups.
- **Greediness**: Patterns are greedy by default; use ? for non-greedy matches where appropriate.
- **No match behavior**: If the pattern matches nothing, the output is identical to the input.

## Troubleshooting
- **Invalid regex pattern**: If processing fails, verify the pattern compiles; check for missing escapes or unbalanced parentheses.
- **Unexpected replacements**: Add anchors (\b, ^, $), make quantifiers non-greedy (?), or refine character classes to limit matches.
- **Case sensitivity issues**: Add inline flag (?i) to the pattern for case-insensitive matches.
- **Multiline text not matching**: Use inline flags like (?m) for multiline anchors or (?s) to let . match newlines, or use character classes like [\s\S].
- **Backreference not working**: Ensure you used double backslashes (e.g., \1) and that the corresponding group exists in the pattern.
- **Nothing changed**: Confirm the pattern actually matches the input text; test with a simpler pattern to validate the pipeline.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/saistringregexsearchreplace/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

