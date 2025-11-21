# Data Monitor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

A multipurpose node to visualize, generate, and transform data. It can display any incoming value, substitute auxiliary variables into text, and output the result as a chosen type. When a passthrough value is connected, it prioritizes that value; otherwise, it processes the manual text.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltdatamonitor.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to inspect and route data between nodes while optionally converting it to a specific type. Typical flows include: previewing intermediate values, formatting text with auxiliary placeholders, evaluating simple formulas, and converting strings to structured types (list, tuple, dict, JSON) before sending them forward.

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
<tr><td style="word-wrap: break-word;">text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Manual text to display and/or convert when no passthrough is provided. Supports variable replacement of %aux%, %aux2%, %aux3%, %aux4%, %aux5% when those inputs are connected.</td><td style="word-wrap: break-word;">User: %aux% \| Score: %aux2% \| Ready: %aux3%</td></tr>
<tr><td style="word-wrap: break-word;">output_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">How to forward the value. Options: ANY (no change), STRING, INT, FLOAT, BOOLEAN, LIST, TUPLE, DICT, JSON, FORMULA.</td><td style="word-wrap: break-word;">JSON</td></tr>
<tr><td style="word-wrap: break-word;">passthrough</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Any value to visualize and forward. If provided, this takes precedence over text (with variable replacement applied if placeholders are present).</td><td style="word-wrap: break-word;">{"a": 1, "b": 2}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary value available for placeholder substitution using %aux%.</td><td style="word-wrap: break-word;">Alice</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary value available for placeholder substitution using %aux2%.</td><td style="word-wrap: break-word;">0.97</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary value available for placeholder substitution using %aux3%.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary value available for placeholder substitution using %aux4%.</td><td style="word-wrap: break-word;">[1, 2, 3]</td></tr>
<tr><td style="word-wrap: break-word;">aux5</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Auxiliary value available for placeholder substitution using %aux5%.</td><td style="word-wrap: break-word;">{"key": "value"}</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">The processed data: either passthrough or text after variable substitution and conversion to the selected output_type.</td><td style="word-wrap: break-word;">{"a":1,"b":2}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Passthrough precedence**: If passthrough is connected, it is processed and forwarded instead of the text input.
- **Variable placeholders**: You can reference auxiliary inputs in text/passthrough using %aux%, %aux2%, %aux3%, %aux4%, %aux5% (case-insensitive).
- **Type conversion**: Selecting output_type attempts to convert the value. ANY forwards as-is; STRING, INT, FLOAT, BOOLEAN cast the value; LIST/TUPLE wrap or convert iterables; DICT expects a key:value,... representation when provided as text; JSON loads a valid JSON string; FORMULA evaluates a simple expression.
- **JSON input**: For JSON, the input must be a valid JSON string (e.g., {"a":1}) or conversion will fail.
- **DICT from text**: For DICT conversion from text, use the format Key1:Value1,Key2:Value2,... with values that can be interpreted as strings/numbers/booleans.
- **Visualization**: The node displays a string representation of the processed value for easy inspection.
- **Output node**: This node can be used as an output/inspection point in a workflow.

## Troubleshooting
- **Type conversion errors**: If the node fails during conversion (e.g., invalid INT/FLOAT/JSON), ensure the input string strictly matches the target format.
- **Empty output**: If passthrough resolves to an empty string after substitution, the node will forward an empty value; check source data and placeholders.
- **Placeholder not replaced**: Verify the correct placeholder name (%aux% to %aux5%) and that the corresponding aux input is connected. Placeholders are case-insensitive.
- **Unexpected list/tuple behavior**: LIST or TUPLE will attempt to convert iterables; for a single value, ensure it’s formatted as desired or use STRING first if needed.
- **BOOLEAN results**: Boolean casting follows standard truthiness rules; if you need strict True/False from strings like 'true'/'false', normalize your text before conversion.
- **JSON/DICT mismatch**: Choose JSON when providing valid JSON syntax. Choose DICT for the simple Key:Value,Key:Value textual format.
