# Compare DataFrames

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node compares two pandas DataFrames and determines whether they are equal under configurable rules. It checks row counts, column sets, per-column value equality, and optionally index equality, using tolerance-aware comparison for numeric data. The node returns both a boolean flag and a structured JSON report that summarizes similarities and differences between the two inputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/comparedataframesnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to validate that two tabular datasets are equivalent, such as in data quality checks, regression tests for ETL pipelines, or comparing production outputs to reference results. Place it downstream of nodes that produce DATAFRAME outputs, for example ExcelToDataFrameNode, ParquetToDataFrameNode, CSVToDataFrameNode, or JsonToDataFrameNode. Connect the `are_equal` boolean to conditional or control-flow nodes to branch on success or failure, and route the `report` string into logging, alerting, or visualization nodes so you can diagnose mismatches. For clearer reports, set `dataframe_1_name` and `dataframe_2_name` to meaningful labels like "expected_results" and "actual_results". Adjust `float_tolerance_digits_after_dot` to account for acceptable floating-point differences, and set `compare_index` depending on whether row index alignment matters for your use case. This node fits well into CI-style validation flows, cross-system reconciliation, and pre-deployment checks for new or refactored data pipelines.

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
<tr><td style="word-wrap: break-word;">dataframe_1</td><td>True</td><td style="word-wrap: break-word;">DATAFRAME</td><td style="word-wrap: break-word;">First DataFrame to compare, typically treated as the baseline or expected dataset. Must be a valid pandas DataFrame produced by upstream nodes.</td><td style="word-wrap: break-word;">A DATAFRAME with columns ["user_id", "balance", "timestamp"] loaded from an archived parquet file of expected daily account balances.</td></tr>
<tr><td style="word-wrap: break-word;">dataframe_2</td><td>True</td><td style="word-wrap: break-word;">DATAFRAME</td><td style="word-wrap: break-word;">Second DataFrame to compare against the first. Any differences in row count, column set, or values relative to `dataframe_1` will be reflected in the equality flag and JSON report.</td><td style="word-wrap: break-word;">A DATAFRAME with columns ["user_id", "balance", "timestamp"] produced by the current day's ETL pipeline run for the same business date.</td></tr>
<tr><td style="word-wrap: break-word;">compare_index</td><td>True</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to include DataFrame indices in the equality check. If true, index values (and index name) must match for the DataFrames to be considered equal; index comparison details are included in the report.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">float_tolerance_digits_after_dot</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of decimal places used when comparing numeric values, including floats and numeric arrays/lists. Both sides are rounded to this many digits before comparison. Must be between 0 and 15.</td><td style="word-wrap: break-word;">6</td></tr>
<tr><td style="word-wrap: break-word;">dataframe_1_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Label used for the first DataFrame inside the JSON report. This improves readability of the `summary`, `columns`, and optional `index` sections but does not affect comparison logic.</td><td style="word-wrap: break-word;">expected_results</td></tr>
<tr><td style="word-wrap: break-word;">dataframe_2_name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Label used for the second DataFrame inside the JSON report. Helps distinguish datasets when reviewing logs or dashboards.</td><td style="word-wrap: break-word;">actual_results</td></tr>
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
<tr><td style="word-wrap: break-word;">are_equal</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">True if the two DataFrames are considered equal under the configured rules: same row count, same set of column names, all shared columns equal row-by-row within the numeric tolerance, and (if enabled) equal index. False if any of these checks fail. Commonly used to drive pass/fail branching or validation gates.</td><td style="word-wrap: break-word;">false</td></tr>
<tr><td style="word-wrap: break-word;">report</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">JSON-formatted string describing the comparison. It contains a `summary` object with row counts and column lists for each named DataFrame plus an overall `are_equal` flag; a `columns` object keyed by column name, where each entry includes presence flags for each DataFrame, whether that column is fully equal, and counts of matching versus mismatching rows; and, if `compare_index` is true, an `index` object with index names and an `are_equal` flag for the index. This structure is suitable for logging, alerting, or downstream JSON parsing.</td><td style="word-wrap: break-word;">{"summary": {"expected_results": {"row_count": 1000, "columns": ["user_id", "balance", "timestamp"]}, "actual_results": {"row_count": 1002, "columns": ["user_id", "balance", "timestamp"]}, "are_equal": false}, "columns": {"user_id": {"exists_in_expected_results": true, "exists_in_actual_results": true, "are_equal": true, "num_rows_match": 1000, "num_rows_mismatch": 2}, "balance": {"exists_in_expected_results": true, "exists_in_actual_results": true, "are_equal": false, "num_rows_match": 998, "num_rows_mismatch": 4}, "timestamp": {"exists_in_expected_results": true, "exists_in_actual_results": true, "are_equal": true, "num_rows_match": 1000, "num_rows_mismatch": 2}}, "index": {"name_in_expected_results": null, "name_in_actual_results": null, "are_equal": false}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node compares values row-by-row for each shared column and then accounts for row-count differences. On very large DataFrames this can be slow and CPU-intensive; for large-scale validation, consider sampling rows, restricting to key columns, or splitting comparisons across multiple runs.
- **Limitations**: Column matching is based on column names, not positions. Any extra or missing columns on either DataFrame will cause the overall `are_equal` flag to be false, even if all shared columns match perfectly.
- **Behavior**: When `compare_index` is true, differing index values, index order, or index name will cause `are_equal` to be false. Index comparison results appear in the `index` section of the JSON report and also influence the global equality decision.
- **Behavior**: Numeric values (including numeric lists and numpy arrays) are compared after rounding to `float_tolerance_digits_after_dot` decimal places and there is explicit handling for NaN and nested arrays. An overly strict tolerance can flag harmless floating noise, while an overly loose tolerance might hide very small but real differences.

## Troubleshooting
- **Equality fails when data looks the same**: Inspect the JSON `report` to see whether specific columns or the index are marked unequal. If differences are only in floating-point precision, increase `float_tolerance_digits_after_dot`. If the index differs but is not important for your logic, disable index comparison with `compare_index = false`.
- **Columns reported as missing or extra**: When the `columns` section shows `exists_in_<name>` as false for some columns, the two DataFrames have different column sets or naming conventions. Align schemas upstream by renaming columns, selecting consistent subsets, or dropping unused fields before comparing.
- **Index differences cause unexpected failures**: If the `index` section has `are_equal` set to false but you only care about the cell values, set `compare_index` to false so the final equality ignores index mismatches.
- **Report output is large or hard to consume**: Wide tables or many columns can generate a sizeable JSON report. To keep logs and dashboards manageable, compare a filtered DataFrame with only critical columns, or post-process the `report` string downstream to retain only the `summary` section for storage or alerting.
