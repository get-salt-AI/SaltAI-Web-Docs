# Input

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node defines a typed input for a workflow and converts its configured value into the selected runtime type before passing it downstream. It supports string, integer, float, boolean, list, and dictionary values, and can optionally allow external callers to override the on-node value at run time while keeping the node value as a fallback.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltinputvalue.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to expose a clean, typed parameter in a workflow without using the legacy input node. It is typically placed near the start of a pipeline to provide configurable values such as prompts, numeric thresholds, flags, JSON objects, or lists that feed into processing nodes downstream.

This node works especially well upstream of nodes that expect primitive or structured values, and it commonly pairs with **Output** for end-to-end parameterized workflows. In deployment scenarios, it is often used as the workflow's public input surface: when **Allow external override** is enabled, API requests, forms, or agent tools can supply a value at execution time; when disabled, the node behaves like a fixed internal constant. Best practice is to use **STRING** for free text, **INT/FLOAT** for validated numeric parameters, and **LIST/DICT** only with valid JSON values so downstream nodes receive properly typed data.

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
<tr><td style="word-wrap: break-word;">input_type</td><td>True</td><td style="word-wrap: break-word;">["STRING", "FLOAT", "INT", "BOOLEAN", "LIST", "DICT"]</td><td style="word-wrap: break-word;">Selects the runtime type this node will output. The configured value is parsed and coerced to this type before execution continues. LIST expects a JSON array string and DICT expects a JSON object string.</td><td style="word-wrap: break-word;">DICT</td></tr>
<tr><td style="word-wrap: break-word;">default_value</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The value used by the workflow. For STRING it is passed through as text; for INT and FLOAT it must be numeric; for BOOLEAN it accepts common truthy or falsy text; for LIST and DICT it must be valid JSON of the correct root type. If external override is enabled, this acts as the fallback value.</td><td style="word-wrap: break-word;">{"region": "west", "team": "sales"}</td></tr>
<tr><td style="word-wrap: break-word;">exposed</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls whether the value can be supplied from outside the workflow at run time. When enabled, external callers can override the node's value; when disabled, the input is fixed and the configured value is always used.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The parsed, typed value produced by the node. Downstream nodes receive the coerced runtime value rather than the raw text entry.</td><td style="word-wrap: break-word;">{"region": "west", "team": "sales"}</td></tr>
<tr><td style="word-wrap: break-word;">ui</td><td style="word-wrap: break-word;">UI metadata</td><td style="word-wrap: break-word;">Execution metadata describing the workflow input, including its generated input id, runtime type, current value, and whether it is externally overrideable.</td><td style="word-wrap: break-word;">{"ui": {"salt_input": [{"id": 27, "name": "input_27", "asset": false, "type": "DICT", "value": "{\"region\": \"west\", \"team\": \"sales\"}", "exposed": true}]}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Behavior**: The node's display name is **Input**, but its class name is `SaltInputValue`; this is the current recommended typed input node, while `SaltInput` is the legacy alternative.
- **Validation**: **LIST** and **DICT** values must be valid JSON with the correct root type. A JSON object entered for LIST, or a JSON array entered for DICT, will fail validation.
- **Validation**: **INT** and **FLOAT** require actual numeric content. Blank strings, whitespace-only values, and non-numeric text raise a clear error instead of silently converting.
- **Integration**: When **Allow external override** is turned off, deployment tooling treats the input as fixed/locked, so agent or API callers cannot supply that value externally.

## Troubleshooting
- **ValueError: LIST SaltInput value must be valid JSON / must be a JSON array**: Enter a valid JSON array such as `["sales", "support"]`, not plain comma-separated text or an object.
- **ValueError: DICT SaltInput value must be a JSON object**: Make sure the value is an object like `{"region": "west"}` and not an array such as `["west"]`.
- **INT/FLOAT SaltInput requires a value / must be a valid integer or number**: Remove blank or non-numeric text and provide a valid numeric value such as `42` or `3.14`. Also avoid boolean values like `true` for numeric types.
