# Rounding Operation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node performs configurable rounding on a single floating-point input. It supports floor, ceil, standard rounding with a specified number of decimal places, and truncation toward zero. It outputs the rounded result as a float and falls back to 0.0 if an invalid operation is specified or an error occurs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/utilities/math/saltroundingoperation.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to convert continuous numeric values into discrete or fixed-precision numbers within a Salt workflow. It is typically placed after numeric computation nodes such as "SaltFloatMathOperation", "SaltAdvancedMathOperation", or statistical aggregators like "SaltStatisticalOperation" to clean up or normalize results. Downstream, its output is commonly consumed by logic and comparison nodes like "SaltComparisonOperation" for threshold checks or branching, or by nodes that format data for dashboards, logs, or external integrations where consistent numeric precision is important. Example patterns include rounding confidence scores to two decimals for UI display, using floor or ceil to compute bucket indices from a score, or using trunc when business rules require dropping fractional parts (such as cutting off extra cents) without changing the sign. In boundary-sensitive logic, explicitly choose floor or ceil; for user-facing output, prefer round with a suitable decimals value.

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
<tr><td style="word-wrap: break-word;">value</td><td>True</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The floating-point number to be rounded. Must lie within the range -1e10 to 1e10. Typically this is an output from an upstream computation or aggregation step and should already be numerically valid.</td><td style="word-wrap: break-word;">3.141592</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">STRING (enum: floor \| ceil \| round \| trunc)</td><td style="word-wrap: break-word;">Determines which rounding rule to apply. "floor" rounds down toward negative infinity, "ceil" rounds up toward positive infinity, "round" performs standard rounding to the nearest value using the specified decimal places, and "trunc" removes the fractional part by moving the value toward zero.</td><td style="word-wrap: break-word;">round</td></tr>
<tr><td style="word-wrap: break-word;">decimals</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Number of decimal places to retain when using the "round" operation. Must be an integer between 0 and 10 inclusive. This parameter is ignored when operation is "floor", "ceil", or "trunc", which always yield integer-magnitude results.</td><td style="word-wrap: break-word;">2</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">The rounded value after applying the selected operation to value. For floor, ceil, and trunc, the magnitude will be an integer (for example, 5.0 or -3.0) represented as a float. For round, the result is a float rounded to the specified number of decimal places. On invalid operations or runtime errors, the result falls back to 0.0.</td><td style="word-wrap: break-word;">3.14</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: This node executes a single scalar rounding operation and is effectively instantaneous, so it can be used freely even in performance-sensitive pipelines.
- **Limitations**: The decimals parameter only affects the round operation. For floor, ceil, and trunc, changing decimals has no effect on the output.
- **Limitations**: The accepted input range is -1e10 to 1e10. Values expected to exceed this range should be normalized or clamped in upstream nodes to avoid configuration problems.
- **Behavior**: If operation is not one of floor, ceil, round, or trunc, the node logs a warning and returns 0.0 instead of throwing an error, which can lead to silent logic issues if not monitored.
- **Behavior**: Even when the mathematical result is an integer (for example, using floor), the output type remains FLOAT (such as 10.0). Downstream components that require integers must explicitly convert the type or use integer-specific nodes.

## Troubleshooting
- **All results are 0.0**: Verify that the operation field is exactly floor, ceil, round, or trunc. Any other value causes the node to default to 0.0 while logging an internal warning.
- **Rounding precision has no effect**: If changing decimals does not change the output, confirm that operation is set to round. For floor, ceil, and trunc, the decimals setting is intentionally ignored.
- **Values appear truncated instead of rounded**: If numbers like 2.9 become 2.0 and -2.9 become -2.0, check whether operation is set to trunc or floor. Switch to round and set an appropriate decimals value to get standard rounding behavior.
- **Downstream type mismatch or integer errors**: If a later node expects an integer, remember that result is a FLOAT. Insert a conversion step or use an integer math node (for example, converting 5.0 to 5) before feeding it into integer-only nodes.
