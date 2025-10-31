# Dynamic Text

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Build a text string from a template by inserting up to 10 variable inputs. It scans the template for placeholders like {{input_1}} through {{input_10}} and replaces them with the provided values. Useful for composing prompts, filenames, or messages that depend on user-supplied or upstream values.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/saltdynamicinput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to assemble a final text from a template and a set of variable fields. Type your template in the text field (press / to see available inputs), connect or type values into input_1..input_10 as needed, and the node will output the compiled string for downstream use.

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
<tr><td style="word-wrap: break-word;">text</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">The template string. Use placeholders {{input_1}} ... {{input_10}} to insert values from the optional inputs. Press / to quickly insert available placeholders.</td><td style="word-wrap: break-word;">User {{input_1}} requested style {{input_2}} with seed {{input_3}}.</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_1}} in the template.</td><td style="word-wrap: break-word;">alice</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_2}} in the template. May be shown after input_1 is set.</td><td style="word-wrap: break-word;">photorealistic</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_3}} in the template. May be shown after input_2 is set.</td><td style="word-wrap: break-word;">12345</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_4}} in the template. May be shown after input_3 is set.</td><td style="word-wrap: break-word;">v2</td></tr>
<tr><td style="word-wrap: break-word;">input_5</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_5}} in the template. May be shown after input_4 is set.</td><td style="word-wrap: break-word;">landscape</td></tr>
<tr><td style="word-wrap: break-word;">input_6</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_6}} in the template. May be shown after input_5 is set.</td><td style="word-wrap: break-word;">high contrast</td></tr>
<tr><td style="word-wrap: break-word;">input_7</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_7}} in the template. May be shown after input_6 is set.</td><td style="word-wrap: break-word;">evening lighting</td></tr>
<tr><td style="word-wrap: break-word;">input_8</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_8}} in the template. May be shown after input_7 is set.</td><td style="word-wrap: break-word;">ISO 100</td></tr>
<tr><td style="word-wrap: break-word;">input_9</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_9}} in the template. May be shown after input_8 is set.</td><td style="word-wrap: break-word;">F/2.8</td></tr>
<tr><td style="word-wrap: break-word;">input_10</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_10}} in the template. May be shown after input_9 is set.</td><td style="word-wrap: break-word;">35mm</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The compiled string after substituting all matching placeholders with provided values.</td><td style="word-wrap: break-word;">User alice requested style photorealistic with seed 12345.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Placeholders**: Use double curly braces around keys exactly as {{input_1}} ... {{input_10}}.
- **Case-sensitive keys**: Placeholder names must match the input field names exactly.
- **Empty values**: If an input is left empty, its placeholder is replaced with an empty string.
- **Progressive reveal**: Additional inputs (input_2..input_10) may appear in the UI only after the prior input is set.
- **No advanced templating**: This node performs simple text replacement and does not support conditionals, loops, or default-value syntax.

## Troubleshooting
- **Output unchanged**: If placeholders remain in the output, verify that they are spelled exactly (e.g., {{input_1}}) and that the corresponding input fields have values.
- **Missing input fields**: If later inputs are not visible, set a value in the preceding input to reveal the next one.
- **Unexpected blanks**: If parts of the template are missing, check for extra spaces or confirm that the intended inputs were actually provided.
- **Literal braces needed**: Literal '{{' or '}}' are interpreted as placeholders; if you need them as plain text, consider using different delimiters or post-processing.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/inputs/saltdynamicinput/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

