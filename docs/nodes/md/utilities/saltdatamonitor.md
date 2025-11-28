# Data Monitor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

A multipurpose node for visualizing, transforming, and forwarding data. It can either pass through incoming data (optionally converting it to a target type) or process manually entered text into a chosen output type. It also supports variable replacement using auxiliary inputs and renders a preview of the processed value.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltdatamonitor.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to inspect and convert data flowing through your workflow, or to generate structured values from text. Typical patterns include: forwarding upstream values while converting them to STRING/INT/FLOAT/BOOLEAN/LIST/TUPLE/DICT/JSON; composing values with placeholders like %aux% using Aux inputs; or evaluating a simple expression via FORMULA. Place it at the end of a branch to visualize outputs or in-line to normalize data types before passing to other nodes.

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
<tr><td style="word-wrap: break-word;">text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Manual input text to be processed when no Passthrough is supplied. Also serves as the displayed preview text. Supports placeholder replacement using Aux inputs.</td><td style="word-wrap: break-word;">Total: %aux% items</td></tr>
<tr><td style="word-wrap: break-word;">output_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Controls how Passthrough/Text is converted before output. Options: ANY, STRING, INT, FLOAT, BOOLEAN, LIST, TUPLE, DICT, JSON, FORMULA.</td><td style="word-wrap: break-word;">JSON</td></tr>
<tr><td style="word-wrap: break-word;">passthrough</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Optional incoming value to visualize and forward. When provided, this takes precedence over the Text field. Placeholders within string-like values can be replaced using Aux inputs.</td><td style="word-wrap: break-word;">User score: %aux%</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary value used for placeholder replacement in Passthrough/Text. Replaces %aux% (case-insensitive).</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary value used for placeholder replacement. Replaces %aux2% (case-insensitive).</td><td style="word-wrap: break-word;">beta</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary value used for placeholder replacement. Replaces %aux3% (case-insensitive).</td><td style="word-wrap: break-word;">3.14</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary value used for placeholder replacement. Replaces %aux4% (case-insensitive).</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">aux5</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary value used for placeholder replacement. Replaces %aux5% (case-insensitive).</td><td style="word-wrap: break-word;">{"k":"v"}</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">The processed value after applying Output Type conversion to either Passthrough (if provided) or Text. Can be string, number, boolean, list, tuple, dict/JSON, or any original value when Output Type is ANY.</td><td style="word-wrap: break-word;">{"total": 42}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The node prioritizes Passthrough over Text: if Passthrough is provided, Text is only used as a visual representation and the output is derived from Passthrough.
- Placeholder replacement is case-insensitive and supports %aux%, %aux2%, %aux3%, %aux4%, and %aux5% within Passthrough/Text.
- Output Type behaviors: ANY forwards the value as-is; STRING casts to text; INT/FLOAT/BOOLEAN cast to numeric/boolean; LIST/TUPLE attempt to convert or wrap into sequences; DICT expects an iterable of key/value pairs; JSON parses a valid JSON string; FORMULA evaluates the string as a Python expression.
- JSON requires valid JSON (for example, keys in double quotes). Invalid JSON will cause a conversion error.
- FORMULA evaluation executes the expression from Text/Passthrough; only use simple, safe expressions you trust.
- When Passthrough is a scalar (e.g., number/boolean), the preview may appear encapsulated, but the actual output type matches the chosen Output Type.

## Troubleshooting
- Conversion failed: Ensure the input is compatible with the selected Output Type. For example, INT/FLOAT require numeric text, and LIST/TUPLE require iterable-compatible content.
- Invalid JSON: Provide a properly formatted JSON string (e.g., {"key": "value"}) before selecting JSON as Output Type.
- DICT conversion not working: DICT expects an iterable of (key, value) pairs or a structure convertible to a dictionary. If starting from text, prefer JSON instead.
- Placeholders not replaced: Confirm you used %aux%, %aux2%, etc., exactly (case-insensitive). Ensure corresponding Aux inputs are connected or set.
- Unexpected output preview: Some scalar values may display as encapsulated in the preview; verify the actual output by inspecting downstream inputs.
- Formula errors: If FORMULA is selected, verify the expression is valid and that any referenced values are provided via placeholders and Aux inputs.
