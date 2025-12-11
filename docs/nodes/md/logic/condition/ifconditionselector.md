# If

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates a condition between inputs A and B and either forwards one of two provided inputs (TRUE_IN/FALSE_IN) or returns a boolean result. Supports lazy evaluation (only the required inputs/branches are executed) and a CUSTOM expression mode where you can define your own logic using A, B, and stored variables.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/ifconditionselector.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to branch workflows based on comparisons or logical tests. When Return Inputs is enabled, wire the values you want to pass forward into TRUE_IN and FALSE_IN; the node will forward exactly one of them based on the condition. When Return Boolean is selected, it outputs True/False so you can drive other logic nodes. Choose CUSTOM if you need an expression beyond the built-in comparisons.

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
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The condition to evaluate. Options include comparisons, identity, membership, bitwise/boolean operations, or CUSTOM for an expression.</td><td style="word-wrap: break-word;">A == B</td></tr>
<tr><td style="word-wrap: break-word;">require_inputs</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true (Return Inputs), the node forwards TRUE_IN or FALSE_IN. If false (Return Boolean), it outputs True/False.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">NOT</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Invert the result after evaluation.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">custom_expression</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Used only when condition is CUSTOM. An expression that can reference A, B, and named global variables from memory storage.</td><td style="word-wrap: break-word;">2*a == 5*b + 2</td></tr>
<tr><td style="word-wrap: break-word;">A</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">First input to evaluate. Type depends on the chosen condition.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">B</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Second input to evaluate. Type depends on the chosen condition.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">TRUE_IN</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Value forwarded if the condition evaluates to True (only used when Return Inputs is enabled).</td><td style="word-wrap: break-word;">Pass</td></tr>
<tr><td style="word-wrap: break-word;">FALSE_IN</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Value forwarded if the condition evaluates to False (only used when Return Inputs is enabled).</td><td style="word-wrap: break-word;">Fail</td></tr>
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
<tr><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">If Return Inputs is enabled, forwards either TRUE_IN or FALSE_IN. Otherwise returns a boolean result.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- When Return Inputs is enabled, you must provide at least one of TRUE_IN or FALSE_IN depending on the branch you expect to use; the node will forward exactly one based on the condition.
- When Return Boolean is selected, TRUE_IN and FALSE_IN are not needed; the node will output True or False.
- CUSTOM expressions can reference A, B (also as a/b), and named global variables from memory storage nodes.
- If the condition is CUSTOM, ensure A and/or B are provided if your expression references them.
- The NOT toggle inverts the evaluated result after the main condition is computed.
- The node uses lazy evaluation: only inputs needed for the selected condition and resulting branch are executed, which can reduce unnecessary computation.
- Membership tests (e.g., A in B) require appropriate types (e.g., B should be iterable).
- Comparison and logical behavior follows standard Python semantics for the chosen operators.

## Troubleshooting
- If the node returns a boolean when you expected a forwarded value, make sure Return Inputs is enabled (require_inputs = true).
- If TRUE_IN/FALSE_IN never execute, check the condition and NOT setting; also verify that your inputs match the expected types for the comparison.
- If a CUSTOM expression does nothing or is disabled, ensure the condition is set to CUSTOM.
- If a CUSTOM expression errors, verify that A/B are provided when referenced and that any referenced storage variables exist.
- If you get unexpected results with equality, ensure A and B are comparable for the selected operator.
- If downstream nodes error when Return Boolean is active, ensure they accept boolean input or switch to Return Inputs and forward appropriate values.
