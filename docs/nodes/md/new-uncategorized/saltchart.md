# Chart

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

SaltChart creates visualizations from TABLE or DATAFRAME inputs, supporting bar, line, scatter, area, histogram, pie, radar, radial bar, treemap, and composed charts. You choose the chart_type and map table columns to axes, labels, and series. The node outputs a rendered chart image and returns the original table unchanged for downstream analytics or additional visualizations.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/saltchart.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use SaltChart to turn tabular data into visual summaries inside a Salt workflow. Typical uses include summarizing aggregated metrics, visualizing time-series trends, exploring distributions, and presenting categorical breakdowns. Place it after nodes that produce or transform tables (for example, SQL query nodes, CSV/Excel importers, or data transformation/aggregation nodes). Connect the image output to report or dashboard nodes, file exporters, or notification/integration nodes that can embed images, while feeding the table output into additional analysis or further chart nodes. Pick bar/line/area for trends and comparisons, scatter for relationships between two numeric variables, histogram for distributions, pie/radialbar/treemap for shares and hierarchy, radar for multi-metric comparisons, and composed to overlay bars and lines. Configure x_column, y_columns, label_column, and value_column according to the chosen chart_type; the UI only exposes the relevant options per type. For best results, ensure numeric fields are correctly typed before charting, keep labels concise but descriptive, and avoid unnecessarily large width/height when generating many charts.

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
<tr><td style="word-wrap: break-word;">table</td><td>True</td><td style="word-wrap: break-word;">TABLE</td><td style="word-wrap: break-word;">Tabular data to visualize. Accepts TABLE or DATAFRAME types and is normalized internally. Must have at least one row; column names referenced by other inputs must exist here.</td><td style="word-wrap: break-word;">A TABLE with columns ['month', 'revenue', 'region'] and rows like {'month': '2024-01', 'revenue': 125000.0, 'region': 'EMEA'}.</td></tr>
<tr><td style="word-wrap: break-word;">chart_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Type of chart to render. One of: 'bar', 'line', 'scatter', 'area', 'histogram', 'pie', 'radar', 'radialbar', 'treemap', 'composed'. This determines which other inputs are relevant.</td><td style="word-wrap: break-word;">bar</td></tr>
<tr><td style="word-wrap: break-word;">x_column</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Column used for the X axis or categories. Required for bar, line, scatter, area, radar, and composed charts. Must exactly match a column name in the input table.</td><td style="word-wrap: break-word;">month</td></tr>
<tr><td style="word-wrap: break-word;">y_columns</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated list of column names used for Y values. For bar, line, area, radar, and composed charts, this can include multiple series (e.g. 'revenue, profit'). For scatter, this should be a single numeric column.</td><td style="word-wrap: break-word;">revenue, profit</td></tr>
<tr><td style="word-wrap: break-word;">column</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Single numeric column for histograms. Only used when chart_type is 'histogram'. Must refer to a numeric column in the table.</td><td style="word-wrap: break-word;">order_value</td></tr>
<tr><td style="word-wrap: break-word;">label_column</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Column providing labels for pie, radial bar, and treemap charts. Only used when chart_type is 'pie', 'radialbar', or 'treemap'. Usually a categorical column such as product category or region.</td><td style="word-wrap: break-word;">product_category</td></tr>
<tr><td style="word-wrap: break-word;">value_column</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Numeric column providing values for pie, radial bar, and treemap charts. Only used when chart_type is 'pie', 'radialbar', or 'treemap'. Values determine slice size or rectangle area.</td><td style="word-wrap: break-word;">total_revenue</td></tr>
<tr><td style="word-wrap: break-word;">title</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional title displayed above the chart. If omitted, the chart renders without a title.</td><td style="word-wrap: break-word;">Monthly Revenue by Region</td></tr>
<tr><td style="word-wrap: break-word;">x_label</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Label for the X axis. If left empty, most chart types fall back to the selected x_column name.</td><td style="word-wrap: break-word;">Month (2024)</td></tr>
<tr><td style="word-wrap: break-word;">y_label</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Label for the Y axis. If left empty, charts often default to the first Y column name or a generic label such as 'Count' for histograms.</td><td style="word-wrap: break-word;">Revenue (USD)</td></tr>
<tr><td style="word-wrap: break-word;">color_column</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Scatter-only option. Column used to color points, adding a categorical or numeric dimension by color. Column must exist in the table.</td><td style="word-wrap: break-word;">region</td></tr>
<tr><td style="word-wrap: break-word;">size_column</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Scatter-only option. Numeric column used to scale point size, adding another quantitative dimension.</td><td style="word-wrap: break-word;">customer_count</td></tr>
<tr><td style="word-wrap: break-word;">group_column</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Bar-only option. Categorical column that splits bars into groups within each X category (grouped or clustered bars).</td><td style="word-wrap: break-word;">region</td></tr>
<tr><td style="word-wrap: break-word;">orientation</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Bar-only option. Orientation of the bars: 'vertical' or 'horizontal'. Affects which axis shows categories versus values.</td><td style="word-wrap: break-word;">horizontal</td></tr>
<tr><td style="word-wrap: break-word;">sort_bars</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Bar-only option. Sort bars by their aggregated values. Options: 'none', 'ascending', or 'descending'. Sorting is by the sum of Y values per category.</td><td style="word-wrap: break-word;">descending</td></tr>
<tr><td style="word-wrap: break-word;">show_markers</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Line/area/composed option. When true, shows point markers on lines, making individual data points more visible.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">bins</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Histogram-only option. Number of bins (bars) in the histogram. 0 enables automatic bin computation. Valid range is 0 to 500.</td><td style="word-wrap: break-word;">30</td></tr>
<tr><td style="word-wrap: break-word;">show_kde</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Histogram-only option. If true, overlays a kernel density estimate curve on top of the histogram to show a smoothed distribution.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">width</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Width of the rendered chart image in pixels. Must be between 200 and 3840. Larger values increase detail but also resource usage.</td><td style="word-wrap: break-word;">1024</td></tr>
<tr><td style="word-wrap: break-word;">height</td><td>False</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Height of the rendered chart image in pixels. Must be between 200 and 2160. Taller charts can better show many categories or dense series.</td><td style="word-wrap: break-word;">640</td></tr>
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
<tr><td style="word-wrap: break-word;">image</td><td style="word-wrap: break-word;">IMAGE</td><td style="word-wrap: break-word;">Rendered chart image produced from the input table and chart configuration. Suitable for saving, embedding in reports, or displaying in UIs.</td><td style="word-wrap: break-word;">An IMAGE object representing a 1024x640 PNG bar chart showing monthly revenue grouped by region.</td></tr>
<tr><td style="word-wrap: break-word;">table</td><td style="word-wrap: break-word;">TABLE</td><td style="word-wrap: break-word;">The original input table, passed through unchanged for use by downstream nodes (for example additional charts, filters, or modeling).</td><td style="word-wrap: break-word;">The same TABLE as was provided on input, with columns like ['month', 'revenue', 'region'] and all original rows.</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Rendering large datasets or high-resolution images can be CPU and memory intensive; consider pre-aggregating data or downsampling and keep width/height moderate for batch workflows.
- **Limitations**: Column names must exist in the input and be suitable for the chart type (e.g., histogram and scatter require numeric columns); otherwise validation inside the chart renderers will raise errors.
- **Behavior**: All string inputs (including column names and labels) are trimmed of leading and trailing whitespace before use, which can resolve subtle naming issues but also normalizes those values.
- **Behavior**: If an unsupported chart_type is provided, the node raises a clear error listing available chart types instead of defaulting, ensuring misconfiguration is visible.

## Troubleshooting
- **Unknown chart type**: If you see an error indicating an unknown chart type, ensure chart_type is exactly one of the supported values like 'bar', 'line', 'scatter', 'area', 'histogram', 'pie', 'radar', 'radialbar', 'treemap', or 'composed'.
- **Column not found**: Errors mentioning that a column does not exist for x_column, y_columns, label_column, or value_column indicate a mismatch with the table schema; verify spelling, case, and that upstream nodes produced the expected columns.
- **Non-numeric data for numeric chart**: If histogram or scatter charts fail with numeric validation errors, the selected column contains non-numeric or incompatible values; clean or cast the data upstream or select another column.
- **Empty or all-null data**: If the table is empty or selected columns are entirely null, validation may fail; adjust upstream filters, joins, or transformations to ensure there is at least some valid data in the chosen columns.
