# Regex Search and Replace

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Finds all substrings in the input text that match a regular expression and replaces them with the provided replacement text. Performs a global replace using standard regex rules, returning the fully transformed string.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saistringregexsearchreplace.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to sanitize, format, or transform text based on patterns (e.g., masking emails, normalizing IDs, replacing tokens). Place it after any node that produces text and before nodes that consume the cleaned or reformatted string.

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
<tr><td style="word-wrap: break-word;">text_input</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The source text where regex matches will be searched and replaced.</td><td style="word-wrap: break-word;">Contact me at john.doe@example.com for details.</td></tr>
<tr><td style="word-wrap: break-word;">regex_pattern</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Regular expression pattern used to find matches. Uses standard Python-style regex syntax.</td><td style="word-wrap: break-word;">\b\w+@\w+\.\w+\b</td></tr>
<tr><td style="word-wrap: break-word;">replacement_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Text to insert in place of each match. Backreferences like \1, \2 can be used to reinsert captured groups.</td><td style="word-wrap: break-word;"><redacted-email></td></tr>
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
<tr><td style="word-wrap: break-word;">replaced_text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The resulting text after applying regex-based replacements across the entire input.</td><td style="word-wrap: break-word;">Contact me at <redacted-email> for details.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Global replacement**: All matches are replaced; there is no option to limit the number of replacements.
- **Regex syntax**: Patterns follow standard regex rules. Flags like case-insensitivity are not exposed; embed inline flags in the pattern if needed (e.g., (?i) for case-insensitive).
- **Escaping**: Remember to escape backslashes in patterns and replacements when entering them as strings (e.g., \\d, \\b).
- **Backreferences**: You can reference captured groups in the replacement text using \1, \2, etc.
- **No multiline/flags inputs**: Multiline or DOTALL behavior must be encoded in the pattern itself using inline modifiers.
- **Large text performance**: Very large inputs or highly complex patterns can impact performance.

## Troubleshooting
- **Invalid regex pattern error**: If the node fails, verify the pattern is valid and properly escaped. Test simpler patterns first.
- **Unexpected replacements**: Ensure your pattern is specific enough and uses anchors or word boundaries (e.g., \b) where appropriate.
- **Backreference not working**: Confirm your pattern defines capturing groups with parentheses and that you reference them correctly (\1, \2).
- **No changes in output**: Check that the pattern actually matches the input; try testing with a known-matching sample or relax the pattern.
- **Case sensitivity issues**: Add inline flags like (?i) at the start of the pattern for case-insensitive matching.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/saistringregexsearchreplace/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

