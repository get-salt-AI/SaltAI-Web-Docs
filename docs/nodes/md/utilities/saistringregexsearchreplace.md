# Regex Search and Replace

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node performs a regular-expression-based search and replace over an input text. You provide the source text, a regex pattern, and a replacement string; it outputs the modified text where all matches of the pattern are replaced. It is useful for precise, pattern-driven text cleanup, normalization, or redaction tasks where simple substring replacement is not sufficient.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saistringregexsearchreplace.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to transform text based on patterns rather than fixed substrings. Typical scenarios include cleaning up model outputs (e.g., removing IDs or emails), normalizing formats (dates, codes, tags), or redacting sensitive segments before sending text to external services.

In a typical workflow, this node sits after a text-generating or text-loading node (for example, a node that retrieves documents, API responses, or LLM outputs as STRING) and before downstream nodes that consume clean text (such as summarization, embedding, or storage nodes). For complex regex-driven pipelines, you might chain it with `SAIStringRegexSearchMatch` (to inspect what will be matched) or other string-processing nodes from `SALT/String/Process/Regex`.

Common integration patterns:
- Upstream: any node outputting `STRING` (e.g., workflow info, prompt builders, API response fetchers).
- Downstream: nodes that analyze, display, or store the sanitized string, or further nodes doing tokenization or splitting.

Best practices:
- Start with simple patterns and test them on sample text to avoid over-matching.
- Use anchors (`^`, `$`), word boundaries (`\b`), and quantifiers carefully.
- When constructing patterns that will be used repeatedly, keep them as general as possible but as specific as needed to avoid unintended replacements.
- Document the intent of each regex in your workflow description so others can safely edit or reuse it.

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
<tr><td style="word-wrap: break-word;">text_input</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The source text in which the regex search and replace will be performed. This can be any plain text such as model outputs, logs, emails, or structured text segments. There is no enforced length limit from the node itself, but extremely large texts may impact performance and memory.</td><td style="word-wrap: break-word;">Dear John,  Your order #12345 placed on 2024-03-10 has been shipped to 123 Main St, Springfield. Tracking ID: ABCD-98765.  Best regards, Acme Inc.</td></tr>
<tr><td style="word-wrap: break-word;">regex_pattern</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A regular expression that defines which parts of `text_input` should be matched and replaced. The example placeholder `\\b\\w{5}\\b` indicates matching any 5-letter word. Patterns must be valid according to the underlying regex engine (Python-style). You can use groups, quantifiers, character classes, and anchors. Be careful with greedy patterns, escaped characters, and multiline behaviors.</td><td style="word-wrap: break-word;">\\b\\d{5}\\b</td></tr>
<tr><td style="word-wrap: break-word;">replacement_text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Text that will replace each match of `regex_pattern` in `text_input`. This can be a static string or can use regex backreferences (e.g., \\1, \\2) if supported by the underlying implementation, allowing you to reinsert captured groups. If you want to remove the matched text entirely, use an empty string.</td><td style="word-wrap: break-word;">[REDACTED-ZIP]</td></tr>
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
<tr><td style="word-wrap: break-word;">replaced_text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The transformed text after applying the regex search and replace operation. Its structure mirrors the original `text_input` but with all matches of `regex_pattern` substituted by `replacement_text`. Downstream nodes should treat this as a clean, ready-for-consumption string.</td><td style="word-wrap: break-word;">Dear John,  Your order #12345 placed on 2024-03-10 has been shipped to 123 Main St, Springfield. Tracking ID: ABCD-98765.  ZIP Code: [REDACTED-ZIP]  Best regards, Acme Inc.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Very large inputs or highly complex regex patterns (especially with nested quantifiers or backtracking-heavy constructs) may significantly slow down execution; consider simplifying patterns or preprocessing large texts.
- **Limitations**: If the regex pattern is invalid (syntax errors, unbalanced groups), the node will fail instead of partially processing; always validate patterns on sample text before production use.
- **Behavior**: The node replaces all matches of the given pattern throughout the text, not just the first occurrence; design your pattern accordingly if you intend to limit replacements.
- **Behavior**: Escaping can be tricky—remember that backslashes may need to be escaped in the workflow configuration (e.g., `\\b` instead of `\b`); mismatched escaping is a common cause of patterns not matching anything.

## Troubleshooting
- **Pattern does nothing / no text changed**: Symptom – output is identical to input. Check that the regex is correctly escaped (double backslashes where needed) and actually matches the sample text; test with a simpler pattern like `test` to confirm the node is running.
- **Error about invalid regular expression**: Symptom – node fails or logs a regex syntax error. Review the pattern for missing brackets, unclosed groups, or unsupported constructs; simplify and reintroduce complexity step by step.
- **Unexpected parts of text replaced**: Symptom – more content than intended is replaced. Likely due to overly broad or greedy patterns (e.g., `.*`). Add anchors, word boundaries, or make quantifiers non-greedy (e.g., `.*?`) and test against smaller samples.
- **Replacement text not using groups as expected**: Symptom – literals like `\1` appear in the output instead of captured text. Ensure the underlying pattern defines capturing groups `( )` and confirm that backreference syntax is correct and properly escaped in the configuration.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/saistringregexsearchreplace/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

