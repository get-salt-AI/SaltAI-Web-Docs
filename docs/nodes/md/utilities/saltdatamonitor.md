# Data Monitor

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Data Monitor lets you inspect values flowing through a workflow, visualize them as text, and optionally convert them to a specific type before forwarding. It supports a passthrough mode, where incoming data is displayed and then transformed, and a manual mode, where you type text that becomes the source value. Auxiliary inputs and a FORMULA output type enable templating and simple computed expressions using other values in the graph.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/saltdatamonitor.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use Data Monitor when you need to debug, inspect, or lightly transform data between nodes. In passthrough mode, connect any upstream value to the "passthrough" input to see a string representation in the node UI; the node then forwards either the original value (ANY) or a converted version (for example, INT, FLOAT, JSON). In manual mode (no passthrough connected), type into the "text" field and choose an "output_type" to turn that text into a typed value, like an integer parameter, a JSON configuration object, or a small formula calculation.

The aux inputs (aux through aux5) are ideal when using the FORMULA output type or string templates—include placeholders like %aux% or %aux2% in your text, and the node will substitute them with the corresponding auxiliary values before conversion. This makes it easy to build dynamic parameters from multiple sources. Place Data Monitor after loaders, API callers, model nodes, or any custom processing step whose output you want to inspect or reshape, and before nodes that require cleanly typed inputs such as configuration, filtering, or routing nodes. Prefer ANY when you only want to observe or log data, and select stricter types only when you are sure of the incoming format to avoid conversion errors.

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
<tr><td style="word-wrap: break-word;">text</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Base text used when no passthrough input is provided. Also acts as the template or expression string for variable substitution and FORMULA evaluation. Its content must be valid for the chosen output_type (for example, numeric text for INT, valid JSON for JSON, or a valid expression for FORMULA).</td><td style="word-wrap: break-word;">{"temperature": 0.4, "top_p": 0.95}</td></tr>
<tr><td style="word-wrap: break-word;">output_type</td><td>True</td><td style="word-wrap: break-word;">ENUM[ANY, STRING, INT, FLOAT, BOOLEAN, LIST, TUPLE, DICT, JSON, FORMULA]</td><td style="word-wrap: break-word;">Controls how the node converts the working value (from passthrough or text). ANY forwards the original value as-is. STRING forces a string. INT, FLOAT, and BOOLEAN coerce into primitives. LIST and TUPLE convert or wrap iterables. DICT converts mapping-like values. JSON parses a JSON string into a dictionary-like object. FORMULA evaluates the final string as a Python expression after any aux substitutions.</td><td style="word-wrap: break-word;">JSON</td></tr>
<tr><td style="word-wrap: break-word;">passthrough</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Optional main input carrying any value you want to visualize or transform. When set, this value is preferred over the text field: it is turned into a string (with aux substitutions) for display and then converted according to output_type for the actual result. Accepts any type, including numbers, strings, lists, tensors, or dictionaries.</td><td style="word-wrap: break-word;">{"user":"alice","plan":"pro","limit":1000}</td></tr>
<tr><td style="word-wrap: break-word;">aux</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">First auxiliary input whose string representation replaces any %aux% placeholder (case-insensitive) in the working string before conversion or evaluation. Useful for parameterizing formulas or templates with a single extra value.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">aux2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Second auxiliary input, used to replace any %aux2% placeholder (case-insensitive) in the working string. Enables templates or formulas that depend on multiple upstream values.</td><td style="word-wrap: break-word;">0.75</td></tr>
<tr><td style="word-wrap: break-word;">aux3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Third auxiliary input, replacing %aux3% (case-insensitive) in the working string. Helpful for more complex templates with several dynamic pieces.</td><td style="word-wrap: break-word;">["news", "sports", "finance"]</td></tr>
<tr><td style="word-wrap: break-word;">aux4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fourth auxiliary input, replacing %aux4% (case-insensitive). Use this when you need an additional external parameter in your template or formula.</td><td style="word-wrap: break-word;">{"max_tokens": 256}</td></tr>
<tr><td style="word-wrap: break-word;">aux5</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Fifth auxiliary input, replacing %aux5% (case-insensitive). Provides one more slot for passing dynamic information into templates or FORMULA expressions.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">output</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The final value after variable substitution and optional type conversion. In passthrough mode with ANY, this is the untouched passthrough input. With other output types, it is the converted result (for example, an int, float, list, JSON-decoded dict, or the evaluated FORMULA value). Downstream nodes can consume it as a standard, correctly typed input.</td><td style="word-wrap: break-word;">{'temperature': 0.4, 'top_p': 0.95}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Large or deeply nested passthrough values must be stringified for visualization, which can be expensive in memory and time; avoid monitoring huge tensors or full datasets unless necessary.
- **Limitations**: JSON mode requires strictly valid JSON syntax; malformed JSON (wrong quotes, trailing commas, or missing braces) will cause parsing to fail and trigger error handling instead of a clean conversion.
- **Limitations**: DICT conversion only works for mapping-like or suitable iterable inputs and does not parse arbitrary colon-separated key:value strings. For textual key/value pairs, prefer JSON or FORMULA.
- **Behavior**: For scalar passthrough values (such as a single number), the node may encapsulate the result in a one-element tuple, which can change how some downstream nodes perceive the type or length of the output.
- **Behavior**: FORMULA evaluates the final text as a Python expression after aux substitutions; invalid expressions raise errors, so restrict usage to simple arithmetic, boolean logic, or basic literal structures that you fully control.
- **Behavior**: The node is marked as an output node and logs debug information, so it may re-run whenever its upstream inputs change, updating both the displayed text and the emitted output accordingly.

## Troubleshooting
- **Error processing passthrough: ...**: The selected output_type is not compatible with the passthrough value (for example, JSON chosen but the value is not valid JSON text). Change output_type to ANY or STRING, or adjust the upstream data to match the expected format.
- **Error processing text: ...**: The text content cannot be converted according to the chosen output_type (for example, non-numeric characters for INT, or invalid JSON). Correct the text or switch to a more appropriate output_type.
- **Unexpected BOOLEAN or LIST results**: BOOLEAN, LIST, and TUPLE follow Python coercion rules (for example, bool("0") is True, list("abc") becomes ["a","b","c"]). If results are surprising, change the input format or use FORMULA to explicitly construct the desired value.
- **Aux substitutions not applied**: Placeholders must be present in the working string and match patterns like %aux% or %aux3% (case-insensitive). If values are not substituted, verify the placeholder names and confirm that the aux inputs are connected with non-null values.
- **Output appears as a tuple instead of a scalar**: Some scalar results are wrapped in a one-element tuple internally. If downstream logic expects a plain scalar, insert an intermediate node or operation to extract the first element.
