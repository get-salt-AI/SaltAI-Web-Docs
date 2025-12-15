# Int Condition

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates a boolean condition between two integers. Supports equality and relational comparisons and outputs a single boolean result. If the operation is invalid or an error occurs, the result defaults to false.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltintconditions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to compare integer values and drive conditional logic in your workflow (e.g., branching, loop control, or enabling/disabling downstream processing). Connect integer-producing nodes to inputs A and B, choose the comparison operator, and use the boolean output to control other nodes.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Left-hand integer operand for the comparison.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Right-hand integer operand for the comparison.</td><td style="word-wrap: break-word;">10</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">["==", "!=", "<", ">", "<=", ">="]</td><td style="word-wrap: break-word;">Comparison operator to apply between A and B.</td><td style="word-wrap: break-word;"><</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean outcome of the selected integer comparison.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Supported operations**: ==, !=, <, >, <=, >= only.
- **Input range**: A and B are constrained to large 64-bit integer-like bounds; values outside UI limits are not permitted.
- **Type strictness**: Inputs must be integers; no implicit casting is performed.
- **Error behavior**: Unknown operations or runtime errors return false.
- **Deterministic**: Given the same inputs, the output is deterministic.

## Troubleshooting
- **Output is always false**: Ensure the operation string exactly matches one of the supported options and that both inputs are valid integers.
- **Cannot select value**: Check that A and B are within the allowed numeric range and step (integer increments).
- **Unexpected result with negatives**: Verify the sign and values of both inputs; comparisons are strictly numeric.
- **Downstream nodes not reacting**: Confirm the boolean output is correctly connected and that any consuming node expects a BOOLEAN type.
