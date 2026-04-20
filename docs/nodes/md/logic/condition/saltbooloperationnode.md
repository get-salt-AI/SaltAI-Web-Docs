# Bool Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node evaluates basic boolean expressions between two boolean inputs. It supports logical AND, OR, XOR, and a unary NOT on the first input, and outputs a single BOOLEAN value. It is intended as a simple building block for controlling conditional flow and flags in Salt logic pipelines.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltbooloperationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Bool Operation node when you need to combine or invert boolean conditions in a workflow. Typical use cases include combining the results of multiple comparison nodes (e.g., ensuring several conditions are true before proceeding), gating functionality (e.g., feature flags that require multiple criteria), or simplifying complex decision logic into a single boolean signal. A common pattern is to take BOOLEAN outputs from nodes like "Int Condition", "Float Condition", "String Condition", or "To Bool" and feed them into Bool Operation. In a typical pipeline, upstream nodes: Int Condition, Float Condition, String Condition, and To Bool generate BOOLEAN outputs. These connect to inputs `a` and `b` of Bool Operation, where you select `a AND b`, `a OR b`, `a XOR b`, or `NOT a`. The resulting BOOLEAN `result` is then fed into control-flow nodes such as If (IfConditionSelector) that use it to decide which branch to execute, or into any node that expects a simple boolean flag. For best practice, keep each Bool Operation node focused on a small, clear part of your logic and name it meaningfully in your graph (e.g., "user_is_eligible" or "can_send_email"). For more complex logic, chain multiple Bool Operation nodes or combine them with condition nodes, rather than trying to encode everything into a single step.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">First boolean operand for the operation. Used as the left-hand side in `a AND b`, `a OR b`, and `a XOR b`, and as the value to invert in `NOT a`. Must be a boolean (true/false), typically provided by a prior condition or conversion node.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Second boolean operand used for `a AND b`, `a OR b`, and `a XOR b`. This input is ignored when the operation is `NOT a`, but still must be connected or provided because it is required at the interface level. Must be a boolean (true/false).</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">op</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">The boolean operation to perform. Allowed values are: "a AND b" for logical conjunction, "a OR b" for logical disjunction, "a XOR b" for exclusive OR, and "NOT a" to invert the first input. Any other value is treated as an unknown operation and results in False.</td><td style="word-wrap: break-word;">"a AND b"</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">The boolean result of applying the selected operation to the inputs. This is a single true/false value suitable for driving conditional routing, enabling or disabling downstream computations, or feeding into nodes that expect a BOOLEAN.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node executes only basic boolean operations and is extremely lightweight, so it can be used freely even in large or complex graphs without noticeable performance impact.
- **Limitations**: The `op` input must be exactly one of "a AND b", "a OR b", "a XOR b", or "NOT a". Any other string triggers a warning internally and causes the output to default to False.
- **Behavior**: When `op` is "NOT a", the `b` input is effectively ignored in the logic, although it is still a required field at the interface level.
- **Behavior**: If an unexpected error occurs during evaluation (for example, if non-boolean types are passed in), the node logs the error and safely returns False instead of raising an error further up the pipeline.

## Troubleshooting
- **Result is always False**: Verify that `op` is set to one of the supported values from the dropdown and is not modified programmatically to an unsupported string. An unknown operation forces the node to return False.
- **Incorrect truth table behavior**: Check that inputs `a` and `b` are actually booleans. If they originate from other data types, ensure they pass through To Bool or an appropriate condition node so that the logic behaves as expected.
- **Unexpected behavior with NOT operation**: Remember that for "NOT a", the node ignores `b`. Ensure that the value you intend to invert is connected to `a` and not to `b`.
- **Chained logic seems wrong**: When combining several Bool Operation nodes, debug by temporarily inspecting the `result` of each node in the chain. This helps pinpoint where the unexpected value first appears and whether the wrong operation or wrong input wiring was used.
