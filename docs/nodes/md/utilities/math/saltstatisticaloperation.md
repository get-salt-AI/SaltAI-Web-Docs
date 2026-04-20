# Statistical Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node performs core statistical calculations on a list of numeric values supplied as a single comma-separated string. It parses the string into floating-point numbers and applies the selected operation (mean, median, mode, standard deviation, variance, sum, product, or count). The node validates the input and returns a single FLOAT output, defaulting to 0.0 for invalid or empty input.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltstatisticaloperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need a quick statistical summary of a sequence of numbers inside a Salt workflow. Typical scenarios include aggregating model scores from multiple runs, summarizing time-series measurements over a window, or computing dispersion metrics (std, variance) to guide conditional routing or alerts. Place it after nodes that generate or collect numeric values (for example, a custom aggregation node or text input converted into numbers) and before nodes that make decisions or further transformations, such as "SaltComparisonOperation" or "SaltFloatMathOperation". The usual pattern is: produce or collect a set of numbers, serialize them as a comma-separated string, send that string into this node with the desired operation, then feed the resulting FLOAT into comparison or threshold nodes, logging, or dashboards. For best results, keep formatting clean (only numeric tokens separated by commas) and choose operations appropriate for your data, such as using "mean" or "median" for central tendency, "std" or "variance" for spread, and "count" when you only care about how many values are present.

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
<tr><td style="word-wrap: break-word;">values</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of numeric values to analyze. Each token is stripped of whitespace and parsed as a float. Tokens that cannot be parsed as floats cause the node to treat the entire input as invalid and return 0.0. Empty tokens, for example from trailing commas, are ignored. At least one valid number is required for a non-zero statistic.</td><td style="word-wrap: break-word;">12.5, 13.0, 11.8, 12.9, 13.2</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING (enum)</td><td style="word-wrap: break-word;">Statistical operation applied to the parsed list of numbers. Allowed values: "mean" (average), "median" (middle value after sorting), "mode" (most frequent value), "std" (population standard deviation), "variance" (population variance), "sum" (total of all values), "product" (product of all values), "count" (number of values). Any unsupported value results in a 0.0 output.</td><td style="word-wrap: break-word;">std</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The computed statistic as a floating-point number. For mean, median, mode, std, variance, sum, and product this is the direct numeric result of the calculation. For count, the number of elements is returned as a float, for example 5 becomes 5.0. This can be chained into other numeric or logical nodes for further processing.</td><td style="word-wrap: break-word;">0.62</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Complexity grows linearly with the number of values; even thousands of entries are typically handled quickly, but extremely long lists increase parsing time and memory usage.
- **Limitations**: If any token in the values string is not a valid float, the node logs a warning and returns 0.0 instead of trying to compute a partial result.
- **Behavior**: Standard deviation and variance are computed as population statistics (dividing by N), which may differ from sample statistics (dividing by N-1) used in some analytic contexts.
- **Behavior**: For the mode operation, if multiple values share the highest frequency, one of them (the first most-common) is returned; ties are not explicitly resolved or reported.
- **Behavior**: When all tokens are empty or whitespace after parsing, the node treats this as having no valid numbers and returns 0.0.

## Troubleshooting
- **Symptom: Result is always 0.0 with a warning about invalid number format**: Some entries in the values string are not valid numbers. Ensure each item is a proper numeric literal, such as 1, -2.5, or 3.14, and remove stray text like "n/a" or "score".
- **Symptom: Result is 0.0 and logs indicate no valid numbers found**: The string may be empty, contain only commas, or use another delimiter. Verify that your list is non-empty and uses commas, for example "1,2,3".
- **Symptom: Unexpected value for std or variance**: Remember that these are population statistics. If you expect sample statistics, adjust your expectations or post-process the result by scaling with N/(N-1) in a downstream math node.
- **Symptom: Mode result seems arbitrary when several values repeat**: The node returns one of the most frequent values without indicating ties. If you need full tie handling, you may need a custom node or external processing to inspect frequency counts.
