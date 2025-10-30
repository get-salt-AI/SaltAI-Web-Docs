# If

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates a condition on inputs A and B and either forwards one of two inputs (TRUE_IN/FALSE_IN) or returns a boolean result. Supports a rich set of comparisons and a CUSTOM expression mode, with optional inversion via NOT. Implements lazy evaluation so only the inputs needed for the selected condition are executed.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/New-Uncategorized/IfConditionSelector.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to branch workflow execution based on dynamic conditions. Connect A and B for comparisons, then either: enable Return Inputs to route TRUE_IN/FALSE_IN through the node based on the condition, or disable it to output a boolean. Choose CUSTOM to write an expression using A/B and available memory variables to define complex conditions.

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
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">The condition to evaluate between A and B or via a custom expression.</td><td style="word-wrap: break-word;">A == B</td></tr>
<tr><td style="word-wrap: break-word;">require_inputs</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Enable to forward TRUE_IN/FALSE_IN based on the result. Disable to return a boolean result instead.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">NOT</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Invert the result after evaluation. For booleans it flips True/False; for non-bool types it applies bitwise inversion.</td><td style="word-wrap: break-word;">False</td></tr>
<tr><td style="word-wrap: break-word;">custom_expression</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Expression used when condition is CUSTOM. You may reference A/a and B/b, and named global variables from memory storage nodes. Most Python-like expressions and built-ins supported.</td><td style="word-wrap: break-word;">2*a == 5*b + 2</td></tr>
<tr><td style="word-wrap: break-word;">A</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">First input to evaluate. Only needed if the selected condition or custom expression references A.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">B</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Second input to evaluate. Only needed if the selected condition or custom expression references B.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">TRUE_IN</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Value to forward if the evaluated condition is True. Required when Return Inputs is enabled and the result is True.</td><td style="word-wrap: break-word;">proceed</td></tr>
<tr><td style="word-wrap: break-word;">FALSE_IN</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Value to forward if the evaluated condition is False. Required when Return Inputs is enabled and the result is False.</td><td style="word-wrap: break-word;">abort</td></tr>
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
<tr><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">If Return Inputs is enabled, forwards TRUE_IN when the condition is True or FALSE_IN when False. If disabled, outputs a boolean result.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Conditions supported**: A is TRUE, B is TRUE, A is NONE, B is NONE, A == B, A != B, A > B, A >= B, A < B, A <= B, A is B, A is not B, A in B, B in A, A & B, A | B, A ^ B, CUSTOM.
- **Lazy evaluation**: Only inputs required by the selected condition or referenced in custom_expression are executed.
- **Return mode**: When Return Inputs is enabled, you must provide TRUE_IN and FALSE_IN. When disabled, the node returns a boolean.
- **CUSTOM expressions**: You may reference A/a and B/b and named global memory variables. If the expression references A or B, those inputs must not be None.
- **Error handling**: If evaluation of the condition raises an error, the node logs it and defaults the result to False.
- **NOT behavior**: Applies logical NOT for boolean results, otherwise bitwise inversion (~) for non-boolean types.

## Troubleshooting
- **Node outputs False unexpectedly**: Check data types of A and B are compatible with the chosen condition (e.g., 'in' expects iterable container). Review logs for any evaluation errors.
- **No output forwarded when Return Inputs is enabled**: Ensure both TRUE_IN and FALSE_IN are connected. The node will forward only the branch that matches the evaluated result.
- **CUSTOM expression disabled**: The custom_expression field is only enabled when condition is set to CUSTOM.
- **CUSTOM uses A/B but result is missing**: Make sure A and/or B are connected and not None if the expression references them.
- **Unexpected bitwise inversion**: If NOT is enabled and the result is not boolean, the node applies ~result. Ensure your condition yields a boolean if you want logical inversion.
