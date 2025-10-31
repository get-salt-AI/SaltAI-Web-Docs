# Int Condition

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates a boolean condition between two integers using a selected comparison operation. Supports equality, inequality, and relational checks. Returns a single boolean indicating the result.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltintconditions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to branch logic or control flow based on integer comparisons, such as validating counters, indices, or thresholds. Typically placed before conditional selectors or loop controllers to determine the next step based on numeric state.

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
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Comparison to apply between a and b. Options: ==, !=, <, >, <=, >=.</td><td style="word-wrap: break-word;">>=</td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">The boolean result of the selected comparison between a and b.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Inputs a and b accept very large integers (up to 64-bit limits).
- If an unknown operation is provided, the node returns false.
- On any internal error, the node safely returns false.

## Troubleshooting
- Result is always false: Ensure the operation is one of ==, !=, <, >, <=, >=.
- Unexpected false for large numbers: Verify a and b are within valid integer ranges and correctly set.
- Used for loop conditions but loop never progresses: Double-check the comparison direction (e.g., use > instead of >=) and the values feeding into a and b.
