# If

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates a specified condition between inputs A and B and returns either a boolean result or one of two provided inputs based on the outcome. Supports a wide range of comparisons and a custom expression mode. Implements lazy evaluation so only inputs required to resolve the condition are computed.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/ifconditionselector.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to branch workflow logic. Choose a condition (e.g., equality, inequality, membership, logical ops) or provide a custom expression. If 'Return Inputs' is enabled, the node forwards TRUE_IN or FALSE_IN depending on the condition result; otherwise it outputs a boolean. Ideal for gating downstream tasks, selecting between alternative inputs, or performing dynamic checks using custom expressions.

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
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The condition to evaluate. Options include: 'A is TRUE', 'B is TRUE', 'A is NONE', 'B is NONE', 'A == B', 'A != B', 'A > B', 'A >= B', 'A < B', 'A <= B', 'A is B', 'A is not B', 'A in B', 'B in A', 'A & B', 'A \| B', 'A ^ B', 'CUSTOM'.</td><td style="word-wrap: break-word;">A == B</td></tr>
<tr><td style="word-wrap: break-word;">require_inputs</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If enabled, forwards TRUE_IN or FALSE_IN to the output based on the evaluated result. If disabled, outputs a boolean instead.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">NOT</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Invert the result after evaluation. Use with care when comparing non-boolean types.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">custom_expression</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Expression evaluated when condition is set to CUSTOM. May reference A, B (also a, b) and named global variables from Memory Storage nodes. Most standard Python-like expressions are supported.</td><td style="word-wrap: break-word;">2*a == 5*b + 2</td></tr>
<tr><td style="word-wrap: break-word;">A</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">First input value to evaluate. Only computed if the chosen condition or custom expression requires it (lazy evaluation).</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">B</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Second input value to evaluate. Only computed if the chosen condition or custom expression requires it (lazy evaluation).</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">TRUE_IN</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Value to forward if the evaluated result is True and 'Return Inputs' is enabled.</td><td style="word-wrap: break-word;">Path A selected</td></tr>
<tr><td style="word-wrap: break-word;">FALSE_IN</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Value to forward if the evaluated result is False and 'Return Inputs' is enabled.</td><td style="word-wrap: break-word;">Path B selected</td></tr>
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
<tr><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">If 'Return Inputs' is enabled: forwards TRUE_IN or FALSE_IN based on the condition. If disabled: returns a boolean representing the evaluation result.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node uses lazy evaluation: only inputs necessary for the chosen condition or custom expression are computed.
- When condition is CUSTOM, A and B referenced in the expression must be provided (not None).
- If 'Return Inputs' is enabled, ensure TRUE_IN and FALSE_IN are connected; otherwise the output may be None depending on the branch taken.
- The NOT option inverts the evaluated result. With non-boolean types, inversion behavior follows the underlying type's semantics; prefer boolean outputs when unsure.
- Membership checks ('A in B', 'B in A') require B or A, respectively, to be a container supporting membership tests.
- Logical operations ('A & B', 'A | B', 'A ^ B') expect types supporting these operators (e.g., booleans, bitwise-capable types).

## Troubleshooting
- Condition always returns False: Verify A and B are provided and of compatible types for the chosen comparison. For CUSTOM, confirm variables (A/a, B/b) are correctly referenced.
- Output is None while 'Return Inputs' is enabled: Ensure the corresponding TRUE_IN or FALSE_IN is connected for the branch taken.
- Type error or unexpected behavior on logical/bitwise ops: Confirm A and B support the selected operator ('&', '|', '^'). Convert or adjust types as needed.
- CUSTOM expression not evaluated: Make sure condition is set to CUSTOM and custom_expression is non-empty. Check syntax and that referenced variables exist.
- Membership tests fail: Ensure the right-hand operand of 'in' is a valid container (e.g., list, dict, string) and types are comparable.
