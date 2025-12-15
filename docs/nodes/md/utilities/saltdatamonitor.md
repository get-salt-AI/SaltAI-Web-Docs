# Data Monitor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

A multipurpose node to visualize, transform, and forward data. It can pass through incoming data, convert it to a specified type, evaluate simple formulas, and substitute auxiliary variables into text. When a passthrough value is connected, it previews the processed value for quick inspection.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltdatamonitor.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to inspect intermediate values, convert data types, or compute simple expressions before passing data onward. Typical workflows include: previewing outputs from other nodes; converting strings to numbers/lists/JSON; evaluating a short expression in FORMULA mode; and templating text with %aux%, %aux2%, etc., sourced from auxiliary inputs.

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
<tr><td style="word-wrap: break-word;">text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base text to process when no passthrough is provided. Supports variable replacement using %aux%, %aux2%, %aux3%, %aux4%, and %aux5% (case-insensitive).</td><td style="word-wrap: break-word;">The value is %aux% units</td></tr>
<tr><td style="word-wrap: break-word;">output_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Controls how the input is processed or converted. Options: ANY, STRING, INT, FLOAT, BOOLEAN, LIST, TUPLE, DICT, JSON, FORMULA.</td><td style="word-wrap: break-word;">JSON</td></tr>
<tr><td style="word-wrap: break-word;">passthrough</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Optional data to visualize and forward. If provided, it is used as the primary input (with optional variable substitution if it is string-like).</td><td style="word-wrap: break-word;">[1, 2, 3]</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary value used for variable substitution in text or passthrough via %aux%.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary value used for variable substitution via %aux2%.</td><td style="word-wrap: break-word;">3.14</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary value used for variable substitution via %aux3%.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary value used for variable substitution via %aux4%.</td><td style="word-wrap: break-word;">{"key": "value"}</td></tr>
<tr><td style="word-wrap: break-word;">aux5</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary value used for variable substitution via %aux5%.</td><td style="word-wrap: break-word;">[10, 20]</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">The processed value after applying variable substitution and conversion as per output_type. If passthrough is connected, this is the transformed passthrough; otherwise, it is the transformed text.</td><td style="word-wrap: break-word;">{"a": 1}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- • Passthrough takes precedence. If provided, it becomes the primary data source; otherwise the node processes the text input.
- • Variable replacement is case-insensitive and supports %aux%, %aux2%, %aux3%, %aux4%, and %aux5% when the source is string-like.
- • output_type behavior: ANY (forward as-is), STRING (str conversion), INT/FLT/BOOLEAN (numeric/boolean conversion), LIST/TUPLE/DICT (collection conversion), JSON (parse from JSON string), FORMULA (evaluate expression).
- • JSON requires valid JSON input; invalid JSON will cause processing errors.
- • FORMULA evaluates a Python expression derived from the input; use with caution and only on trusted content.
- • Some scalar results may be internally wrapped for display purposes; the actual output remains the converted value.
- • When passthrough is a tensor or a non-string object, variable substitution does not apply to the object itself; it is forwarded or converted according to output_type.

## Troubleshooting
- • JSON parsing fails: Ensure the input is valid JSON (e.g., use double quotes and proper structure).
- • Type conversion errors (INT/FLOAT/BOOLEAN/LIST/TUPLE/DICT): Provide inputs compatible with the chosen type. For dictionaries, prefer JSON or an iterable of key-value pairs.
- • Unexpected TRUE for BOOLEAN: Non-empty strings evaluate to True; use exact "true"/"false" strings with JSON mode if you need strict booleans.
- • Formula errors or unsafe content: Verify your expression is valid and safe. Avoid untrusted inputs in FORMULA mode.
- • No visible substitution: Variable placeholders only replace when the source is string-like and matches %aux%, %aux2%, %aux3%, %aux4%, or %aux5% (case-insensitive).
