# Dynamic Text

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a text string by inserting up to 10 user-provided values into placeholders. Placeholders use the format {{input_1}} through {{input_10}} and are replaced with the corresponding optional inputs. Useful for templating prompts, messages, or any dynamic text assembly.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/saltdynamicinput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to compose a text template that references up to 10 dynamic variables. Type your template into the required text field and insert variables using the slash menu to ensure exact placeholder names. Provide values for input_1 to input_10 as needed; unset inputs default to empty strings. The node outputs the compiled text for downstream nodes.

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
<tr><td style="word-wrap: break-word;">text</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">The template string containing placeholders like {{input_1}} ... {{input_10}} to be replaced with provided values.</td><td style="word-wrap: break-word;">Hello {{input_1}}, your order {{input_2}} is ready.</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_1}} in the template.</td><td style="word-wrap: break-word;">Alice</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_2}} in the template. Hidden until input_1 is set.</td><td style="word-wrap: break-word;">#12345</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_3}} in the template. Hidden until input_2 is set.</td><td style="word-wrap: break-word;">tomorrow</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_4}} in the template. Hidden until input_3 is set.</td><td style="word-wrap: break-word;">10:00 AM</td></tr>
<tr><td style="word-wrap: break-word;">input_5</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_5}} in the template. Hidden until input_4 is set.</td><td style="word-wrap: break-word;">Pickup</td></tr>
<tr><td style="word-wrap: break-word;">input_6</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_6}} in the template. Hidden until input_5 is set.</td><td style="word-wrap: break-word;">Main Store</td></tr>
<tr><td style="word-wrap: break-word;">input_7</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_7}} in the template. Hidden until input_6 is set.</td><td style="word-wrap: break-word;">Priority</td></tr>
<tr><td style="word-wrap: break-word;">input_8</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_8}} in the template. Hidden until input_7 is set.</td><td style="word-wrap: break-word;">Thank you!</td></tr>
<tr><td style="word-wrap: break-word;">input_9</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_9}} in the template. Hidden until input_8 is set.</td><td style="word-wrap: break-word;">Contact support if needed.</td></tr>
<tr><td style="word-wrap: break-word;">input_10</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Value to replace {{input_10}} in the template. Hidden until input_9 is set.</td><td style="word-wrap: break-word;">support@example.com</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The compiled text with all {{input_n}} placeholders replaced by the provided values.</td><td style="word-wrap: break-word;">Hello Alice, your order #12345 is ready.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Placeholder format**: Use double curly braces with exact keys: {{input_1}} through {{input_10}}.
- **Available options menu**: In the template field, press '/' to insert available placeholders reliably.
- **Unset inputs**: Any unset input is treated as an empty string, so its placeholder will be removed if present.
- **Replacement behavior**: Performs simple text replacement; all occurrences of a placeholder are replaced without escaping.
- **Case sensitivity**: Placeholder keys are case-sensitive and must exactly match input_1 ... input_10.
- **Progressive visibility**: input_2 is hidden until input_1 is set, input_3 until input_2 is set, and so on.
- **Limit**: Supports up to 10 variables. For more variables, chain multiple Dynamic Text nodes.

## Troubleshooting
- **Placeholders not replaced**: Ensure the template uses exact names like {{input_1}} and that you provided corresponding input values.
- **Unexpected leftover braces**: Provide values for the referenced inputs or remove unused placeholders from the template.
- **Need more than 10 variables**: Split your template across multiple nodes and pass the compiled result forward.
- **Wrong value inserted**: Verify that you didn't reuse the wrong placeholder name and check the order of your inputs.
- **Multiple occurrences not all replaced**: This node replaces all matches; if only some changed, confirm the exact placeholder text matches the input key (no extra spaces).

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/inputs/saltdynamicinput/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

