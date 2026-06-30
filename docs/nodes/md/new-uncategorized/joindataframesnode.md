# Join DataFrames

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Join DataFrames merges two DATAFRAME inputs into a single DATAFRAME using SQL-style join behavior. It supports shared-column joins, different left/right key column names, configurable suffixes for overlapping columns, and cross joins when no key relationship is needed.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/joindataframesnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to combine two tabular datasets by matching rows on one or more key columns. Common scenarios include joining customer records with orders by `customer_id`, merging product metadata into transaction logs by `sku`, or combining model results with reference labels by `sample_id`. It is typically placed after data-loading nodes such as **CSV to DataFrame**, **Excel to DataFrame**, **JSON to DataFrame**, or **Parquet to DataFrame**, and before output or analysis nodes such as **DataFrame to CSV**, **DataFrame to HTML**, **Compare DataFrames**, or **DataFrame Columns to List**. Use `on` when both DataFrames share the same key column names; use `left_on` and `right_on` when the key columns differ between the two datasets. For best results, inspect or standardize column names before joining, choose the smallest join type that preserves the rows you need, and use explicit suffixes so overlapping columns remain easy to interpret downstream.

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
<tr><td style="word-wrap: break-word;">left_df</td><td>True</td><td style="word-wrap: break-word;">DATAFRAME</td><td style="word-wrap: break-word;">The left DataFrame in the join operation. For `left` joins, all rows from this DataFrame are preserved; for `inner` joins, only rows with matching keys are retained.</td><td style="word-wrap: break-word;">A customer table with columns: customer_id, customer_name, region</td></tr>
<tr><td style="word-wrap: break-word;">right_df</td><td>True</td><td style="word-wrap: break-word;">DATAFRAME</td><td style="word-wrap: break-word;">The right DataFrame in the join operation. For `right` joins, all rows from this DataFrame are preserved; matching columns are combined with the left DataFrame based on the configured join keys.</td><td style="word-wrap: break-word;">An orders table with columns: order_id, customer_id, order_total, order_date</td></tr>
<tr><td style="word-wrap: break-word;">how</td><td>True</td><td style="word-wrap: break-word;">inner \| left \| right \| outer \| cross</td><td style="word-wrap: break-word;">The join strategy to apply. `inner` keeps only matching rows, `left` keeps all left rows, `right` keeps all right rows, `outer` keeps all rows from both sides, and `cross` creates every possible row combination.</td><td style="word-wrap: break-word;">left</td></tr>
<tr><td style="word-wrap: break-word;">on</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated column name or names to join on when the key columns have the same names in both DataFrames. Leave empty if using `left_on` and `right_on`. Not allowed for cross joins.</td><td style="word-wrap: break-word;">customer_id</td></tr>
<tr><td style="word-wrap: break-word;">left_on</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated key column name or names from the left DataFrame. Use together with `right_on` when the join keys have different names. The number of columns must match `right_on`.</td><td style="word-wrap: break-word;">customer_id,region_code</td></tr>
<tr><td style="word-wrap: break-word;">right_on</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated key column name or names from the right DataFrame. Use together with `left_on` when the join keys have different names. The number of columns must match `left_on`.</td><td style="word-wrap: break-word;">buyer_id,shipping_region</td></tr>
<tr><td style="word-wrap: break-word;">suffixes</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Two comma-separated suffixes applied to overlapping non-key column names from the left and right DataFrames. Must contain exactly two values.</td><td style="word-wrap: break-word;">_customers,_orders</td></tr>
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
<tr><td style="word-wrap: break-word;">joined_df</td><td style="word-wrap: break-word;">DATAFRAME</td><td style="word-wrap: break-word;">The merged DataFrame produced by the selected join operation. It contains rows and columns from both inputs, with overlapping non-key columns renamed using the configured suffixes.</td><td style="word-wrap: break-word;">A joined table with columns: customer_id, customer_name, region, order_id, order_total, order_date</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Join keys are required except for cross joins**: Provide either `on` or both `left_on` and `right_on` unless `how` is set to `cross`.
- **Cross joins can grow very quickly**: A cross join returns every combination of rows, so 10,000 left rows and 5,000 right rows would produce 50,000,000 rows.
- **Suffixes must be exactly two values**: The `suffixes` input must contain two comma-separated suffixes, such as `_left,_right`; otherwise the node raises an error.
- **Column names must match exactly**: Join column names are case-sensitive and whitespace-sensitive after comma splitting, so ensure your DataFrame columns are clean and consistent.

## Troubleshooting
- **`Either 'on' or both 'left_on' and 'right_on' must be provided`**: Add a shared key column to `on`, or provide matching key columns in both `left_on` and `right_on`. For a Cartesian product, set `how` to `cross` and leave all key fields empty.
- **`left_on and right_on must have the same number of columns`**: Make sure both fields contain the same number of comma-separated key columns, such as `customer_id,region` and `buyer_id,shipping_region`.
- **`Cross join cannot be combined with on/left_on/right_on`**: Clear the `on`, `left_on`, and `right_on` fields when using `how = cross`.
- **Unexpected duplicate or renamed columns**: If both DataFrames contain non-key columns with the same names, configure `suffixes` with meaningful labels like `_customers,_orders` so downstream nodes can identify each source.
