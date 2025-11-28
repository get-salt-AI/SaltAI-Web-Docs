# If

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates a condition between inputs A and B and routes the flow accordingly. It can either forward one of two inputs (TRUE_IN or FALSE_IN) or output a boolean result. Supports lazy evaluation so only the inputs needed for the chosen condition are computed.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/ifconditionselector.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to branch logic in a workflow. Choose a condition (e.g., equality, comparison, containment, identity, bitwise, or a custom expression). If you enable 'Return Inputs', the node forwards TRUE_IN when the condition is true, otherwise FALSE_IN. If you disable it, the node outputs a boolean. Useful for gating expensive operations, selecting between alternatives, or building complex conditional flows with custom expressions.

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
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Select the condition to evaluate. Options: A is TRUE, B is TRUE, A is NONE, B is NONE, A == B, A != B, A > B, A >= B, A < B, A <= B, A is B, A is not B, A in B, B in A, A & B, A \| B, A ^ B, CUSTOM.</td><td style="word-wrap: break-word;">A == B</td></tr>
<tr><td style="word-wrap: break-word;">require_inputs</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true (Return Inputs), forwards TRUE_IN or FALSE_IN based on the evaluated condition. If false (Return Boolean), returns a boolean result instead.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">NOT</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Invert the result after evaluation. For booleans, applies logical NOT; for non-boolean results that support it, applies bitwise inversion.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">custom_expression</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Expression used only when condition is CUSTOM. You can refer to A and B (case-insensitive) and named global variables from memory storage nodes. Most standard Python-like expressions are supported.</td><td style="word-wrap: break-word;">2*a == 5*b + 2</td></tr>
<tr><td style="word-wrap: break-word;">A</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">First input to evaluate. Only required/used if the selected condition (or custom expression) references A.</td><td style="word-wrap: break-word;">42</td></tr>
<tr><td style="word-wrap: break-word;">B</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Second input to evaluate. Only required/used if the selected condition (or custom expression) references B.</td><td style="word-wrap: break-word;">100</td></tr>
<tr><td style="word-wrap: break-word;">TRUE_IN</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Value forwarded to the output when the condition evaluates to true. Only used when require_inputs is true.</td><td style="word-wrap: break-word;">forward this value if true</td></tr>
<tr><td style="word-wrap: break-word;">FALSE_IN</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Value forwarded to the output when the condition evaluates to false. Only used when require_inputs is true.</td><td style="word-wrap: break-word;">forward this value if false</td></tr>
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
<tr><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">If require_inputs is true, forwards TRUE_IN when the condition is true, otherwise FALSE_IN. If require_inputs is false, outputs a boolean result.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Lazy evaluation**: Only inputs referenced by the selected condition (or present in the custom expression) are evaluated, allowing you to avoid unnecessary computation.
- **Return mode**: With 'Return Inputs' enabled (require_inputs = true), TRUE_IN/FALSE_IN must be provided; otherwise, the node returns a boolean.
- **Custom expressions**: When using CUSTOM, ensure A and/or B are provided if referenced in the expression. You can also reference global memory variables by name.
- **Inversion behavior**: NOT inverts booleans using logical NOT; for non-boolean results supporting inversion, bitwise NOT is applied.
- **Comparison handling**: Equality and inequality are designed to work with a variety of data types, including tensors and standard Python types.
- **Type compatibility**: For operators like >, <, &, |, ^, ensure A and B support those operations; otherwise, the condition will fail and default to false.

## Troubleshooting
- **No output forwarded when 'Return Inputs' is on**: Connect both TRUE_IN and FALSE_IN. If one branch isn't connected and is selected by the condition, the node cannot forward a value.
- **custom_expression disabled**: Set condition to CUSTOM to enable editing and use of the custom expression input.
- **Condition always false in CUSTOM**: Verify A and/or B are connected if referenced (e.g., expression contains 'a' or 'b'), and check for typos in variable names.
- **Type errors (e.g., '>' not supported)**: Use compatible types for the chosen operator or switch to a condition that matches your data types.
- **Unexpected inversion**: If NOT is enabled and the result is non-boolean, bitwise inversion is applied which may not match logical expectations. Disable NOT or ensure the result is boolean.
- **Containment checks fail ('A in B' / 'B in A')**: Ensure the right-hand operand supports containment (e.g., list, set, dict, string) and that types are comparable.
