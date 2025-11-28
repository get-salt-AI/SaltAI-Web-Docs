# Dynamic Text

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a text string by replacing placeholders with values from up to 10 optional inputs. Placeholders use the format {{input_1}} ... {{input_10}}, and all occurrences are globally replaced using simple string substitution.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/saltdynamicinput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to compose prompts, messages, or metadata strings that need to inject variable values at runtime. Enter a template in the text field and reference any of the optional inputs via {{input_n}}. Connect or set the optional inputs you need; fields progressively appear as you set previous ones.

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
<tr><td style="word-wrap: break-word;">text</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">Template text that can reference variables using {{input_1}} to {{input_10}}. The editor supports quick selection of available variables.</td><td style="word-wrap: break-word;">User: {{input_1}} requested {{input_2}} items.</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to substitute for {{input_1}}.</td><td style="word-wrap: break-word;">Alice</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to substitute for {{input_2}}. This field becomes visible after input_1 is set.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to substitute for {{input_3}}. Visible after input_2 is set.</td><td style="word-wrap: break-word;">priority</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to substitute for {{input_4}}. Visible after input_3 is set.</td><td style="word-wrap: break-word;">express</td></tr>
<tr><td style="word-wrap: break-word;">input_5</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to substitute for {{input_5}}. Visible after input_4 is set.</td><td style="word-wrap: break-word;">blue</td></tr>
<tr><td style="word-wrap: break-word;">input_6</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to substitute for {{input_6}}. Visible after input_5 is set.</td><td style="word-wrap: break-word;">medium</td></tr>
<tr><td style="word-wrap: break-word;">input_7</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to substitute for {{input_7}}. Visible after input_6 is set.</td><td style="word-wrap: break-word;">USD</td></tr>
<tr><td style="word-wrap: break-word;">input_8</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to substitute for {{input_8}}. Visible after input_7 is set.</td><td style="word-wrap: break-word;">2025-01-01</td></tr>
<tr><td style="word-wrap: break-word;">input_9</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to substitute for {{input_9}}. Visible after input_8 is set.</td><td style="word-wrap: break-word;">note</td></tr>
<tr><td style="word-wrap: break-word;">input_10</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to substitute for {{input_10}}. Visible after input_9 is set.</td><td style="word-wrap: break-word;">thanks</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The final compiled text after replacing all matching placeholders with provided input values.</td><td style="word-wrap: break-word;">User: Alice requested 3 items.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Placeholder format**: Use double braces with exact key names, e.g., {{input_1}}. Keys are case-sensitive.
- **Global replacement**: All occurrences of a given placeholder are replaced throughout the text.
- **Optional inputs**: Only set the inputs you need. Unused placeholders remain unchanged.
- **Progressive visibility**: input_2 shows after input_1 is set; this pattern continues up to input_10.
- **No expression parsing**: Substitution is plain text replacement; it does not evaluate formulas or nested placeholders.
- **Limit of 10 variables**: Only input_1 through input_10 are supported.

## Troubleshooting
- **Variables not replacing**: Confirm the placeholder uses double braces and exact key name (e.g., {{input_3}}) and that the corresponding input is not empty.
- **Fields not visible**: Set the previous input to reveal the next (e.g., set input_1 to show input_2).
- **Unexpected leftover {{...}}**: Provide values for the missing inputs or remove those placeholders from the template.
- **Empty output**: Ensure the required 'text' template is not empty; optional inputs default to empty strings if not set.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/inputs/saltdynamicinput/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

