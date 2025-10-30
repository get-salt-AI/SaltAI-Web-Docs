# Data Monitor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

A multipurpose data utility that displays and forwards data. It can either pass through incoming data or process text you enter, converting it to a requested type. Supports variable substitution from auxiliary inputs and shows the processed value in the node’s panel.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/SaltDataMonitor.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to inspect, transform, and forward data within a workflow. Connect any value to Passthrough to visualize it and forward it as-is or converted to a target type. If no Passthrough is connected, type a value or expression in Text and choose an Output Type to generate data. Aux inputs can inject values into the Text via %aux%, %aux2%, %aux3%, %aux4%, %aux5% placeholders, useful for building formulas or templated strings.

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
<tr><td style="word-wrap: break-word;">text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Text to visualize or process when no Passthrough is provided. Can include placeholders like %aux%, %aux2%, %aux3%, %aux4%, %aux5% that will be replaced by the corresponding Aux inputs.</td><td style="word-wrap: break-word;">The result is %aux% + %aux2%</td></tr>
<tr><td style="word-wrap: break-word;">output_type</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Desired output type or behavior. Options: ANY (forward as-is), STRING, INT, FLOAT, BOOLEAN, LIST, TUPLE, DICT, JSON, FORMULA.</td><td style="word-wrap: break-word;">FORMULA</td></tr>
<tr><td style="word-wrap: break-word;">passthrough</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Any value to visualize and forward. When provided, this takes precedence over Text. The value can be converted according to Output Type before being forwarded.</td><td style="word-wrap: break-word;">[1, 2, 3]</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Auxiliary value used for placeholder substitution (%aux%) in Text, often combined with FORMULA or templated strings.</td><td style="word-wrap: break-word;">7</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second auxiliary value for %aux2% substitution.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third auxiliary value for %aux3% substitution.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fourth auxiliary value for %aux4% substitution.</td><td style="word-wrap: break-word;">{"key":"value"}</td></tr>
<tr><td style="word-wrap: break-word;">aux5</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fifth auxiliary value for %aux5% substitution.</td><td style="word-wrap: break-word;">3.14</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The processed value. If Passthrough is present, it is forwarded (optionally converted) and displayed. Otherwise, the processed Text is returned.</td><td style="word-wrap: break-word;">12  (when Text='7+5' and Output Type='FORMULA')</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Passthrough precedence**: If Passthrough is connected, it overrides Text. The UI will display a readable representation of the incoming value after any variable substitution and type conversion.
- **Variable substitution**: Placeholders %aux%, %aux2%, %aux3%, %aux4%, %aux5% in Text are replaced with the corresponding Aux values (case-insensitive).
- **Output types**: ANY forwards data unchanged; STRING/INT/FLOAT/BOOLEAN/LIST/TUPLE/DICT/JSON convert the data; FORMULA evaluates the Text as a Python expression. The input must be compatible with the chosen type.
- **DICT and JSON expectations**: DICT requires an input that is already a mapping or an iterable of key-value pairs. For string-based dictionaries, prefer JSON (must be valid JSON).
- **Error handling**: If conversion or evaluation fails, the node logs the error and returns the unconverted value where possible.
- **Display behavior**: Scalars and booleans may be displayed encapsulated, but the output still matches the selected type.

## Troubleshooting
- **Text is not used**: Ensure Passthrough is disconnected; Passthrough takes priority over Text.
- **Placeholders not replaced**: Verify the placeholders are spelled as %aux%, %aux2%, %aux3%, %aux4%, %aux5% and that the corresponding Aux inputs are connected. Replacement is case-insensitive but the placeholder format must match.
- **JSON conversion fails**: Confirm the Text or Passthrough contains valid JSON (e.g., proper quotes and braces).
- **DICT conversion fails**: Provide a mapping or an iterable of key-value pairs. If starting from a string, parse it into a JSON object first and use JSON output type.
- **FORMULA errors**: Ensure the expression is valid and all referenced placeholders are replaced with suitable values.
- **Unexpected booleans or numeric casting**: Remember that non-empty strings cast to BOOLEAN become True, and numeric strings must be parseable for INT/FLOAT.
