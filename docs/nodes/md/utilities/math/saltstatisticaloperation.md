# Statistical Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Computes a single statistical metric from a comma-separated list of numbers. Supports common measures such as mean, median, mode, standard deviation, variance, sum, product, and count. The node parses the input string into numbers, applies the selected operation, and returns the result as a float.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltstatisticaloperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a quick statistical summary from a list of numeric values provided as text (e.g., dynamic inputs, parameters, or outputs from other nodes formatted as strings). Typical workflow: feed a comma-separated list (spaces are allowed) into 'values', choose the desired 'operation', and use the resulting number downstream for thresholds, normalization, or reporting.

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
<tr><td style="word-wrap: break-word;">values</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of numbers to analyze. Whitespace around numbers is allowed. Example format: "1,2,3,4,5" or "1.5, 2.0, 3.25".</td><td style="word-wrap: break-word;">3, 3, 4, 10, 12.5</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">The statistical operation to apply to the parsed numbers.</td><td style="word-wrap: break-word;">mean</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed statistical result of the chosen operation. Always returned as a float.</td><td style="word-wrap: break-word;">3.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input parsing**: The node expects a comma-separated string of numeric values. Non-numeric entries or malformed numbers cause the node to return 0.0.
- **Empty input**: If no valid numbers are found after parsing, the node returns 0.0.
- **Population formulas**: Standard deviation and variance are computed using population definitions (divide by N), not sample definitions (which divide by N-1).
- **Median behavior**: For an even number of values, the median is the mean of the two middle sorted values.
- **Mode behavior**: If multiple values tie for most frequent, the first encountered most frequent value is returned.
- **Product growth**: The product can grow or underflow rapidly; large magnitude results may be subject to floating-point precision limits.
- **Type of count**: Although 'count' is conceptually an integer, it is returned as a float to match the output type.

## Troubleshooting
- **Result is 0.0 unexpectedly**: Ensure 'values' contains only numbers separated by commas (use '.' for decimals) and no empty entries like trailing commas.
- **Mode seems inconsistent on ties**: When multiple values share the highest frequency, only one (the first most frequent encountered) is returned.
- **Standard deviation/variance mismatch**: If you expect sample statistics, note this node uses population formulas. Convert externally if needed.
- **Very large or very small results**: Operations like 'product' can overflow or underflow. Consider scaling inputs or using fewer values.
