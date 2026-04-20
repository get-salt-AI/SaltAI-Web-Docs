# Regex Search and Match

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node scans an input string using a user-provided regular expression and collects every match it finds. It returns a list of all matching substrings, making it easy to extract patterns like emails, IDs, or specific word forms. It is read-only and does not modify the original text.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saistringregexsearchmatch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to pull structured or semi-structured data out of free-form text using regex. Common scenarios include extracting all email addresses from a support transcript, pulling ticket or order IDs from log lines, collecting hashtags from social media text, or finding all words that match a particular pattern (for example, exactly 6 letters or starting with a capital letter). It typically sits downstream of nodes that generate or load text (such as a node that reads a document, merges multiple strings, or formats an LLM response) and upstream of nodes that process lists (for example, nodes that deduplicate values, validate formats, or map each match into another structure). It complements "SAIStringRegexSearchReplace": you can first use Search and Match to verify your pattern and see what is being matched, then use Search and Replace in a parallel or subsequent path to actually transform the text. When designing workflows, keep regex complexity modest on very large inputs and test patterns incrementally before applying them to long documents.

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
<tr><td style="word-wrap: break-word;">text_input</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The full text to search with the regular expression. Accepts any standard string, including multi-line content. The pattern is applied to the entire string; very large texts are supported but may impact performance depending on pattern complexity.</td><td style="word-wrap: break-word;">Order log: #12345 placed by john.doe@example.com #12346 placed by anna_smith@sample.org Payment failed for #12347 (user: test.user@example.net)</td></tr>
<tr><td style="word-wrap: break-word;">regex_pattern</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A regular expression pattern in Python `re` syntax used to find matches in the text. Supports character classes, quantifiers, anchors, groups, and inline flags. Invalid patterns (such as unbalanced parentheses or incorrect escape sequences) will cause an error. Remember that in many UIs backslashes must be escaped (for example, use "\\b[a-zA-Z]{6}\\b" to match any 6-letter word).</td><td style="word-wrap: break-word;">#[0-9]{5}</td></tr>
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
<tr><td style="word-wrap: break-word;">matches</td><td style="word-wrap: break-word;">LIST</td><td style="word-wrap: break-word;">A list containing every match of the regex in the input text. If there are no matches, this will be an empty list. When the pattern has no capturing groups, each list item is the matched substring as a STRING. When the pattern uses capturing groups, Python's `re.findall` semantics apply: with one group, each item is that group's match; with multiple groups, each item is a tuple of group matches. Downstream list-processing nodes can iterate over or transform these values.</td><td style="word-wrap: break-word;">['#12345', '#12346', '#12347']</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node runs a regex search over the entire input string; large texts combined with complex or poorly constrained patterns (especially those that cause heavy backtracking) can significantly slow execution.
- **Limitations**: The regex must be valid according to Python's `re` engine. Syntax errors like unclosed groups or invalid escape sequences will raise an exception instead of returning an empty match list.
- **Behavior**: If your pattern contains capturing groups, the shape of the output list changes based on those groups (strings vs tuples). This can affect downstream nodes that assume simple STRING elements.
- **Behavior**: Matching is case-sensitive by default. To perform case-insensitive matches, encode this into the pattern itself (for example, `(?i)pattern`), since there is no separate flag input on the node.

## Troubleshooting
- **Common Error 1**: Regex compilation error such as "bad escape" or "unterminated group". This usually means the pattern string is malformed or not properly escaped in the UI. Double-check all backslashes and parentheses, and try simplifying the pattern until it compiles.
- **Common Error 2**: The node returns an empty list even when you expect matches. Verify that the pattern actually fits the text (correct case, no unintended anchors like `^` or `$`, correct quantifiers) and test your regex on a very small sample of the input first.
- **Common Error 3**: Downstream nodes complain about unexpected data types (for example, tuples instead of strings). This typically occurs when the regex contains multiple capturing groups. Use non-capturing groups `(?:...)` where you do not need group outputs, or adapt downstream logic to handle tuples.
- **Common Error 4**: Workflow becomes slow after adding this node with a complex pattern. Simplify the regex, avoid nested quantifiers like `.*.*`, or pre-trim the input text with upstream nodes so that the search runs on a smaller substring.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/utilities/saistringregexsearchmatch/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

