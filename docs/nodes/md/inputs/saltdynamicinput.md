# Dynamic Text

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node composes a final text string from a dynamic template and up to ten string inputs. You configure a template and provide input_1 through input_10, which are substituted directly into the template. It is useful for constructing prompts, messages, or any text that needs runtime variable interpolation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/inputs/saltdynamicinput.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to dynamically assemble text from multiple variable parts, such as building language model prompts, email bodies, or parameterized instructions. Place it downstream of nodes that produce strings (for example, Salt Workflow Input nodes configured as STRING, text-processing nodes, or logic/branching nodes) and upstream of nodes that consume a single text input (such as model inference, routing, or logging nodes). In the template text field, write your base text and reference the inputs as variables (e.g., one variable for topic, another for audience, another for tone). The optional input_1–input_10 fields appear progressively in the UI: input_2 is shown once input_1 has a value, input_3 after input_2, and so forth, which keeps configuration tidy while still allowing up to ten parameters. Typical patterns include: stitching together multiple upstream outputs into a single coherent prompt, combining user-entered metadata (topic, tone, length) into one instruction block, or standardizing message formats by varying only the variable portions. Common downstream consumers are model-inference nodes that expect a single STRING prompt and output nodes that log or display the composed text.

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
<tr><td style="word-wrap: break-word;">text</td><td>True</td><td style="word-wrap: break-word;">DYNAMIC_STRING</td><td style="word-wrap: break-word;">Template text that will be processed as a dynamic string. It can reference up to ten variables named input_1 through input_10. At execution time the node will substitute each variable name that matches a provided input value. The node itself does not enforce length limits, but very long templates may affect downstream processing.</td><td style="word-wrap: break-word;">Write a detailed blog post about the topic from input_1, targeting the audience in input_2, using the tone described in input_3.</td></tr>
<tr><td style="word-wrap: break-word;">input_1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">First variable value available to the template. This is always visible and is usually the main dynamic parameter (for example, a topic or subject). It is substituted wherever the template refers to the first input variable.</td><td style="word-wrap: break-word;">sustainable urban gardening</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Second variable value, shown only after input_1 has been set. Use this when your template needs a second parameter such as audience, channel, or language.</td><td style="word-wrap: break-word;">beginner home gardeners in small apartments</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Third variable value, shown after input_2 is set. Often used for tone, style, or format guidance in your template.</td><td style="word-wrap: break-word;">friendly and inspirational</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Fourth variable value, revealed once input_3 has a value. Only needed if your template references a fourth parameter such as language or medium.</td><td style="word-wrap: break-word;">English</td></tr>
<tr><td style="word-wrap: break-word;">input_5</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Fifth variable value, revealed once input_4 has a value. Use this for additional constraints like target length, format, or structure.</td><td style="word-wrap: break-word;">approximately 1500 words</td></tr>
<tr><td style="word-wrap: break-word;">input_6</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Sixth variable value, revealed once input_5 has a value. Optional and only relevant if your template expects a sixth parameter.</td><td style="word-wrap: break-word;">include practical step-by-step instructions</td></tr>
<tr><td style="word-wrap: break-word;">input_7</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Seventh variable value, revealed once input_6 has a value. Optional; useful for extra qualifiers or constraints.</td><td style="word-wrap: break-word;">avoid technical jargon</td></tr>
<tr><td style="word-wrap: break-word;">input_8</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Eighth variable value, revealed once input_7 has a value. Optional; often used for SEO or channel-specific details.</td><td style="word-wrap: break-word;">optimize for the keyword "balcony garden"</td></tr>
<tr><td style="word-wrap: break-word;">input_9</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Ninth variable value, revealed once input_8 has a value. Optional; for additional instructions or metadata.</td><td style="word-wrap: break-word;">include three engaging title options</td></tr>
<tr><td style="word-wrap: break-word;">input_10</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Tenth and final variable value, revealed once input_9 has a value. Optional; use when you need one more structured input for your template.</td><td style="word-wrap: break-word;">end with a clear call-to-action to subscribe to the newsletter</td></tr>
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
<tr><td style="word-wrap: break-word;">text</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The fully assembled text after substituting all provided input_1–input_10 values into the template. The result is a plain string that can be consumed directly by downstream nodes that accept STRING input, such as model-inference nodes, routers, or output/display nodes.</td><td style="word-wrap: break-word;">Write a detailed blog post about sustainable urban gardening targeting beginner home gardeners in small apartments using a friendly and inspirational tone. The article should be approximately 1500 words, include practical step-by-step instructions, avoid technical jargon, optimize for the keyword "balcony garden", and end with a clear call-to-action to subscribe to the newsletter.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Simple replacement only**: The node performs straightforward text substitution using the provided input_1–input_10 values; it does not support conditionals, loops, or expression evaluation.
- **Missing values**: If your template references a variable for which no value is provided, that reference will remain unchanged in the output, which can lead to partially filled text if you forget to set an input.
- **Progressive UI**: Optional inputs are shown progressively (input_2 after input_1, etc.) purely for usability; missing inputs do not stop execution but simply leave the corresponding variable text unchanged.
- **String semantics**: All incoming values are treated as strings for substitution. If an upstream node outputs a non-string type, convert it to STRING first to avoid unexpected formatting in the final text.

## Troubleshooting
- **Unreplaced variable segments**: If parts of the template still appear as variable names in the output, verify that the template’s variable names exactly match the input fields and that those inputs are not empty.
- **Unexpected final text structure**: When the composed text looks malformed (for example, doubled spaces or missing punctuation), review the template around variable insertion points and adjust spacing and punctuation to account for optional inputs that may be empty.
- **Downstream node rejects the output**: If a downstream node reports a type error, confirm that you have connected this node’s "text" output to a STRING-compatible input and that the downstream node indeed expects plain text.
- **Difficulty managing many variables**: If you find the configuration unwieldy with many inputs, consider grouping related data upstream (for example, concatenating topic and subtopic into a single STRING) so that fewer input_n fields are needed here.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../images/assets/inputs/saltdynamicinput/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

