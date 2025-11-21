# Comparison Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Performs numeric comparisons and clamping between two floating-point values. It returns both a numeric result and a boolean indicating the outcome of comparison-type operations. Supports min, max, clamping, and relational checks with a small tolerance for equality.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltcomparisonoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to select min/max values, clamp a value within bounds, or to evaluate relational conditions that can drive downstream logic (e.g., gating branches). For comparison modes, the boolean output indicates whether the relation holds, while the numeric output mirrors 'a' when true and 0.0 when false.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Primary numeric input to compare or clamp. For comparison modes, this is the value returned when the comparison is true.</td><td style="word-wrap: break-word;">3.14</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Secondary numeric input used for min/max selection and for all relational comparisons against 'a'. Not used when operation is 'clamp'.</td><td style="word-wrap: break-word;">2.0</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">["min", "max", "clamp", "equal", "greater", "less", "greater_equal", "less_equal"]</td><td style="word-wrap: break-word;">Selects the operation to perform. 'min'/'max' choose between a and b. 'clamp' bounds 'a' between clamp_min and clamp_max. Relational operations evaluate the relationship between a and b.</td><td style="word-wrap: break-word;">greater_equal</td></tr>
<tr><td style="word-wrap: break-word;">clamp_min</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Lower bound used only when operation is 'clamp'. Ensures the result is not less than this value.</td><td style="word-wrap: break-word;">0.0</td></tr>
<tr><td style="word-wrap: break-word;">clamp_max</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Upper bound used only when operation is 'clamp'. Ensures the result is not greater than this value.</td><td style="word-wrap: break-word;">1.0</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Numeric outcome. For min/max: the selected value. For clamp: 'a' clamped to [clamp_min, clamp_max]. For comparisons: returns 'a' if the condition is true, otherwise 0.0.</td><td style="word-wrap: break-word;">3.14</td></tr>
<tr><td style="word-wrap: break-word;">comparison_result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean outcome for comparison modes (equal/greater/less/greater_equal/less_equal). For min/max/clamp, this is always False.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Tolerance for equality**: 'equal' uses an absolute tolerance of 1e-9 when comparing a and b.
- **Result behavior on comparisons**: For relational operations, the numeric 'result' equals 'a' when the condition is true; otherwise it is 0.0.
- **Clamp ignores b**: In 'clamp' mode, only 'a', 'clamp_min', and 'clamp_max' are used; 'b' is ignored.
- **Bounds and precision**: Inputs accept FLOAT values approximately within [-1e10, 1e10] with step 0.001.
- **Boolean for non-comparison modes**: 'comparison_result' is always False for 'min', 'max', and 'clamp'.

## Troubleshooting
- **Unexpected 0.0 result**: In comparison modes, this indicates the condition evaluated to False. Verify 'a' and 'b' or choose a different operation.
- **Equality false due to tiny differences**: 'equal' uses a 1e-9 tolerance. If your values differ beyond that, they won't be considered equal. Adjust inputs or handle tolerance upstream.
- **Clamp not behaving as expected**: Ensure clamp_min <= clamp_max. Reversed bounds can yield unintuitive results due to the max(min()) logic.
- **Boolean always False**: This is expected for 'min', 'max', and 'clamp'. Use a relational operation if you need a True/False indicator.
- **Out-of-range inputs**: If UI prevents setting desired values, check the allowed range [-1e10, 1e10] and step size 0.001.
