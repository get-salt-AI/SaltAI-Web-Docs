# Statistical Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Computes a statistical measure from a comma-separated list of numbers. Supports mean, median, mode, standard deviation, variance, sum, product, and count. If inputs are invalid or empty, the node returns 0.0.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltstatisticaloperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to quickly summarize numeric data provided as a single comma-separated string. Commonly placed after text or configuration inputs to compute aggregates for downstream logic, thresholds, or reporting.

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
<tr><td style="word-wrap: break-word;">values</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of numbers to analyze. Whitespace is allowed and ignored around numbers.</td><td style="word-wrap: break-word;">1, 2, 2, 3, 4.5</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">["mean", "median", "mode", "std", "variance", "sum", "product", "count"]</td><td style="word-wrap: break-word;">Statistical operation to apply to the parsed list of numbers.</td><td style="word-wrap: break-word;">median</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed statistic. For count, the value represents the number of parsed entries; for other operations, the corresponding statistic.</td><td style="word-wrap: break-word;">2.0</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input parsing**: The node expects a comma-separated string of numeric values; non-numeric tokens cause the node to return 0.0.
- **Empty input handling**: If the list contains no valid numbers after parsing, the result is 0.0.
- **Standard deviation and variance**: These are population metrics (dividing by n, not n-1).
- **Mode behavior**: If multiple modes exist, the first most-frequent value encountered is returned; ties are not resolved deterministically for equal frequencies.
- **Types**: All values are parsed as floats. The output type is FLOAT even for operations like count.
- **Product growth**: Large products can overflow to very large magnitudes; ensure inputs are within reasonable ranges.

## Troubleshooting
- **Result is 0.0 unexpectedly**: Verify the values string contains only numbers separated by commas (e.g., "1,2,3"). Remove extra text or symbols.
- **Unknown operation warning**: Ensure the operation is one of: mean, median, mode, std, variance, sum, product, count.
- **Mode seems incorrect**: With multiple equally frequent values or floating-point values that are nearly equal, the selected mode may differ from expectations. Consider rounding inputs before passing them.
- **Standard deviation differs from expected**: This node uses the population standard deviation. If you need sample standard deviation, adjust your workflow accordingly.
- **Count not an integer**: The output type is FLOAT; if you need an integer, cast downstream or use a node that converts types.
