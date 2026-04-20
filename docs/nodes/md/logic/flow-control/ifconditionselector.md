# If

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

The IfConditionSelector node evaluates a selected logical or comparison condition between inputs A and B, or a custom expression, and routes data based on whether the result is true or false. It can forward one of two arbitrary inputs (TRUE_IN or FALSE_IN) or return a boolean result directly. The node uses lazy evaluation so that only the inputs required for the active condition or custom expression are actually computed.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/flow-control/ifconditionselector.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use IfConditionSelector when you need conditional branching or decision-making in a Salt workflow. Typical scenarios include picking between two prompts based on a numeric score, switching between two processing branches depending on a flag, or enabling a section of the pipeline only when stored state meets certain criteria. Upstream, connect any values you want to compare into A and B (numbers, booleans, strings, or other comparable objects) and connect branch values into TRUE_IN and FALSE_IN (for example, alternative prompts, images, or configuration objects). Place this node in the control/logic layer of your pipeline: its single output then feeds into the next node that should receive either the chosen branch value or the boolean decision. With require_inputs set to true, it behaves like a router that forwards TRUE_IN or FALSE_IN. With require_inputs set to false, it behaves as a pure condition evaluator that outputs a boolean which can feed into other logic nodes or additional IfConditionSelector instances. When you need more advanced rules, use the CUSTOM condition with custom_expression referencing A, B, and Memory Storage variables for stateful or compound conditions.

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
<tr><td style="word-wrap: break-word;">condition</td><td>True</td><td style="word-wrap: break-word;">STRING (choice)</td><td style="word-wrap: break-word;">Selects the condition to evaluate between A and B. Options: "A is TRUE", "B is TRUE", "A is NONE", "B is NONE", "A == B", "A != B", "A > B", "A >= B", "A < B", "A <= B", "A is B", "A is not B", "A in B", "B in A", "A & B", "A \| B", "A ^ B", and "CUSTOM". A and B must be of types that support the chosen operation (for example, numbers for > and <, booleans or bitwise-compatible types for &, \|, ^, and container-like types for membership tests).</td><td style="word-wrap: break-word;">A >= B</td></tr>
<tr><td style="word-wrap: break-word;">require_inputs</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Controls the output mode. When true (Return Inputs), the node forwards TRUE_IN if the condition evaluates to true and FALSE_IN if it evaluates to false; in this mode TRUE_IN and FALSE_IN should be connected. When false (Return Boolean), TRUE_IN and FALSE_IN are ignored and the node outputs the boolean result of the evaluated condition or custom expression.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">NOT</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If enabled, inverts the result after the condition or custom expression is evaluated. For boolean results it flips true to false and false to true. For non-boolean results that support bitwise inversion, it applies a bitwise NOT, which can significantly alter the value and should be used with care.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">custom_expression</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Expression evaluated when condition is set to "CUSTOM". It can reference A, a, B, b, and any named global variables from Memory Storage nodes. A and B must not be None if they are referenced. The environment supports a safe subset of Python-like operators and built-ins. This field is disabled when condition is not "CUSTOM".</td><td style="word-wrap: break-word;">2 * a == 5 * b + 2</td></tr>
<tr><td style="word-wrap: break-word;">A</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">First operand for the selected condition or custom expression. It may be any value, but must be compatible with the chosen condition: for example, a numeric score for A > B, a boolean or truthy/falsy value for "A is TRUE", or an element/container in membership-related checks or custom logic.</td><td style="word-wrap: break-word;">0.85</td></tr>
<tr><td style="word-wrap: break-word;">B</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Second operand for the selected condition or custom expression. As with A, its type must match the expectations of the chosen condition, such as another numeric value for comparisons, a boolean for logical or bitwise operations, or a list/string for membership tests like "A in B".</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">TRUE_IN</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Value forwarded when the evaluated condition is true and require_inputs is true. This can be any data type, such as a text prompt, image, or configuration object. The node only needs to compute TRUE_IN when the true branch is selected, taking advantage of lazy evaluation.</td><td style="word-wrap: break-word;">"ultra-detailed portrait, dramatic lighting"</td></tr>
<tr><td style="word-wrap: break-word;">FALSE_IN</td><td>False</td><td style="word-wrap: break-word;">ANY</td><td style="word-wrap: break-word;">Value forwarded when the evaluated condition is false and require_inputs is true. As with TRUE_IN, it can be any type and is only computed if the false branch is taken.</td><td style="word-wrap: break-word;">"simple portrait, soft lighting"</td></tr>
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
<tr><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">ANY or BOOLEAN</td><td style="word-wrap: break-word;">Single output of the node. When require_inputs is true, the output is TRUE_IN if the condition is true, otherwise FALSE_IN, passed through unchanged. When require_inputs is false, the output is a boolean representing the evaluation result of the condition or custom expression.</td><td style="word-wrap: break-word;">With require_inputs=true: "ultra-detailed portrait, dramatic lighting"; with require_inputs=false: true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs lazy evaluation and only computes A, B, TRUE_IN, or FALSE_IN when they are required by the selected condition and branch, which can significantly reduce unnecessary computation in large or multi-branch workflows.
- **Limitations**: For the CUSTOM condition, A and B must not be None if they are referenced in custom_expression; if they are missing, evaluation cannot proceed correctly and the condition will effectively be treated as false or skipped.
- **Behavior**: The require_inputs flag changes the output contract: when enabled, downstream nodes should expect the same type as TRUE_IN/FALSE_IN; when disabled, they must handle a boolean result instead.
- **Behavior**: The NOT flag is applied after the base condition or custom expression is evaluated. While this is straightforward for booleans, bitwise inversion on other types may yield non-intuitive results and should be used carefully.
- **Limitations**: If A and B are incompatible with the selected condition (for example, using A > B with non-comparable objects, or "A in B" where B is not a container), internal errors are logged and the condition result defaults to false.

## Troubleshooting
- **Condition always evaluates to false**: Verify that A and B are connected and have compatible types for the selected condition, and check that the NOT flag is not unintentionally inverting your intended logic.
- **CUSTOM expression has no effect**: Ensure condition is set to "CUSTOM", custom_expression is non-empty, and A and B are non-None and correctly referenced (for example, as a and b) when used in the expression.
- **Downstream node type mismatch**: If a downstream node errors because it receives an unexpected type, review the require_inputs setting. Use require_inputs=false if the downstream node expects a boolean, and true if it expects a forwarded data object from TRUE_IN/FALSE_IN.
- **Errors for membership or bitwise operators**: If logs mention errors around operations like "A in B" or "A & B", check that A and B are appropriate (element vs container for membership, or integers/booleans for bitwise operations) and adjust your inputs or chosen condition accordingly.
