# Dynamic Text

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node lets you compose a text template and inject up to ten dynamic values into it using named placeholders. You write a base string and reference optional input slots (input_1…input_10) with {{input_n}} markers. At runtime the node replaces each placeholder with the corresponding input value and outputs the fully compiled string.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/saltdynamicinput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to construct dynamic prompts, messages, or configuration strings that depend on upstream values. Place it after nodes that generate or collect text fragments (for example, user inputs from Salt Workflow Input, extracted fields, or earlier LLM outputs), and before nodes that consume a final, fully formed string (such as model invocation, HTTP calls, or storage/logging nodes). In the text field, type your base template and insert placeholders like {{input_1}}; then connect other nodes to the optional input_1–input_10 fields. Inputs are revealed progressively: input_2 appears only after input_1 is set, input_3 after input_2, and so on, helping keep the UI compact. This node works particularly well combined with Salt Workflow Input (to supply user-provided values), text-processing nodes (to preprocess pieces), and downstream model or action nodes that require a single, well-structured text argument. Prefer this node over manual string concatenation when you want clearer, more maintainable prompt templates or messages.

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
<tr><td style="word-wrap: break-word;">text</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">Template string that can include placeholders referencing optional inputs, using the syntax {{input_1}} … {{input_10}}. The UI provides a variable picker and guidance to help you insert these. Any placeholder that does not have a corresponding input value will simply remain unchanged in the output. Supports arbitrary text length; avoid excessively large templates to keep workflows readable.</td><td style="word-wrap: break-word;">Dear {{input_1}}, your order {{input_2}} has been shipped to {{input_3}}. Estimated delivery: {{input_4}}.</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">First optional dynamic value. When set, it will replace all occurrences of {{input_1}} in the template. If left empty, the placeholder remains as-is. Typically connected to a user name, title, or primary variable.</td><td style="word-wrap: break-word;">Alex</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The final compiled text after replacing any matching placeholders {{input_1}}…{{input_10}} with their corresponding input values. The output is a plain string that can be passed directly into downstream nodes that accept text, such as model invocation, routing, logging, or notification nodes.</td><td style="word-wrap: break-word;">Dear Alex, your order #483920 has been shipped to Berlin, Germany. Estimated delivery: May 12, 2026.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs straightforward placeholder replacement and is very fast even for fairly long templates; overhead is negligible compared to model calls.
- **Limitations**: Only fixed keys input_1–input_10 are supported; you cannot dynamically create more placeholders beyond these ten slots.
- **Behavior**: Replacement is literal and case-sensitive—only exact matches like {{input_1}} are replaced; mismatched or misspelled placeholders are left unchanged.
- **Behavior**: If an input value is an empty string, its placeholder is replaced with an empty string, which can result in double spaces or awkward punctuation if not handled in the template.

## Troubleshooting
- **Placeholders not replaced**: If you still see {{input_1}} in the output, verify that the template uses the exact key name (including braces) and that the corresponding input_n field is connected or filled.
- **Hidden inputs not visible**: input_2–input_10 appear only after the preceding input is set; to reveal input_3, ensure input_2 has been given a value or connected to an upstream node.
- **Unexpected leftover braces in output**: This happens when there are placeholders with no corresponding value or when braces are used for other purposes; either supply values or adjust the template text to avoid accidental {{...}} patterns.
- **Output format issues**: If your compiled text has extra spaces or broken formatting, check how punctuation and spaces are arranged around placeholders, especially for optional inputs that may be empty.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/inputs/saltdynamicinput/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

