# Comparison Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltComparisonOperation compares two floating point values using several common operations (min, max, clamp, and relational comparisons). It outputs a float result plus a boolean comparison_result that indicates whether the chosen condition is true. For pure comparison modes, the float result is either the first input value or 0.0 depending on whether the comparison passed, making it easy to gate downstream logic.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltcomparisonoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to compare two numeric values or bound a value within a range as part of a Salt workflow. Typical scenarios include thresholding model scores, clamping configuration values into safe ranges, or conditionally routing data based on numeric conditions. It usually receives FLOAT values from upstream utility/math nodes such as SaltFloatMathOperation, SaltLogarithmicOperation, SaltAdvancedMathOperation, or SaltStatisticalOperation, or from other processing nodes that output scalar scores or metrics.

Place this node in the control/utility part of your pipeline: compute or fetch your numeric values upstream, compare or clamp them with SaltComparisonOperation, then feed the boolean comparison_result into conditional or branching logic nodes (such as switch/selector or gating nodes), and the float result into further math or configuration nodes. For example, you might compute a confidence score with a model node, then use SaltComparisonOperation with operation="greater_equal" and a threshold b=0.8, and finally send comparison_result to a routing node that decides whether to send the sample into a "high-confidence" or "low-confidence" processing branch.

For clamping, use operation="clamp" with clamp_min and clamp_max to enforce safe numeric ranges (for example, keep learning rates, temperature values, or weights between allowed bounds). For min/max operations, you can select the smaller or larger of two signals and pass it as the numeric result, ignoring the boolean output. As a best practice, choose the operation that matches your intention explicitly (for example, greater_equal instead of greater if equal values should count as passing) and keep your value ranges within the documented min/max to avoid unexpected clipping.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">First float operand to compare or clamp. Must be between -1e10 and 1e10. This is the primary value that will be passed through as the numeric result for comparison operations when the condition is true.</td><td style="word-wrap: break-word;">0.87</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Second float operand used as the reference value in comparisons, such as a threshold, baseline, or alternative value. Must be between -1e10 and 1e10. Used directly for min/max and as the right-hand side for relational operators.</td><td style="word-wrap: break-word;">0.8</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Specifies which comparison behavior to apply. Options: "min" (returns the smaller of a and b), "max" (returns the larger of a and b), "clamp" (clamps a into [clamp_min, clamp_max]), "equal" (a approximately equals b within a small tolerance), "greater" (a > b), "less" (a < b), "greater_equal" (a >= b), "less_equal" (a <= b). For non-clamp operations, clamp_min and clamp_max are ignored.</td><td style="word-wrap: break-word;">greater_equal</td></tr>
<tr><td style="word-wrap: break-word;">clamp_min</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Lower bound used only when operation is set to "clamp". The output will be at least this value: result = max(clamp_min, min(a, clamp_max)). Valid range is -1e10 to 1e10. Ignored for non-clamp operations.</td><td style="word-wrap: break-word;">0.0</td></tr>
<tr><td style="word-wrap: break-word;">clamp_max</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Upper bound used only when operation is set to "clamp". The output will be at most this value: result = max(clamp_min, min(a, clamp_max)). Valid range is -1e10 to 1e10. Ignored for non-clamp operations.</td><td style="word-wrap: break-word;">1.0</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Primary numeric output of the comparison. For "min" and "max", this is the minimum or maximum of a and b. For "clamp", this is a clamped into [clamp_min, clamp_max]. For relational operations (equal, greater, less, greater_equal, less_equal), this is a when the condition is true, otherwise 0.0. Downstream nodes can use this both as a value and as a simple numeric gate (non-zero when the condition passed).</td><td style="word-wrap: break-word;">0.87</td></tr>
<tr><td style="word-wrap: break-word;">comparison_result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean flag indicating the outcome of the comparison for relational and equality operations. For "equal", "greater", "less", "greater_equal", and "less_equal" this is true when the condition holds and false otherwise. For "min", "max", and "clamp" it is always false, since these are treated as pure numeric operations. Use this output to drive conditional routing or branching logic.</td><td style="word-wrap: break-word;">true</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node operates on scalar floats only and is very fast; it is safe to use freely even in complex or deeply nested pipelines.
- **Limitations**: Equality uses a fixed tolerance of 1e-9 for float comparisons, so very small differences below that threshold will be treated as equal.
- **Behavior**: For relational operations (equal, greater, less, greater_equal, less_equal), the float result is a if the condition is true, otherwise 0.0; do not treat result as a generic min or max in those modes.
- **Behavior**: For non-relational operations (min, max, clamp) the comparison_result output is always false, so downstream logic that depends on the boolean must not rely on it in those modes.
- **Behavior**: clamp_min and clamp_max are only used when operation is set to "clamp"; changing them has no effect when using the other comparison operations.

## Troubleshooting
- **Condition seems ignored (comparison_result always false)**: Check that you are not using a non-relational operation like "min", "max" or "clamp"; these always output comparison_result = false by design. Switch to a relational operation such as "greater_equal" if you need a boolean gate.
- **Unexpected equal behavior for near values**: The "equal" operation uses a small tolerance of 1e-9 for floating point comparison, so values that differ by less than this are treated as equal. If two values that you expect to be different evaluate as equal, increase the scale of your numbers or adjust upstream computations to produce more separation.
- **Result is always 0.0 for relational operations**: This happens when the comparison condition is never satisfied. Verify that you set a and b correctly (for example, a should be your measured value and b your threshold) and that you chose the appropriate operator (for example, use "greater_equal" if equality should also pass). You can also inspect comparison_result directly instead of relying solely on the numeric result.
- **Clamp not respecting bounds**: If the result is not what you expect when using "clamp", ensure that clamp_min is less than or equal to clamp_max and that you actually selected operation="clamp". Also verify that the value a is within the configured global range (-1e10 to 1e10); out-of-range values may already be constrained by the input settings.
