# Int Condition

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Evaluates a comparison between two integers and returns a boolean result. Supports equality, inequality, and standard relational operators. If an unknown operation is provided or an error occurs, the node safely returns False.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltintconditions.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to compare two integer values to drive logic in your workflow, such as branching, gating, or loop control. Typical scenarios include checking counters, thresholds, or iteration indices to determine the next step.

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
<tr><td style="word-wrap: break-word;">a</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Left-hand integer operand for the comparison. Supports very large positive and negative values.</td><td style="word-wrap: break-word;">5</td></tr>
<tr><td style="word-wrap: break-word;">b</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Right-hand integer operand for the comparison. Supports very large positive and negative values.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">operation</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Comparison to perform between a and b. One of: ==, !=, <, >, <=, >=.</td><td style="word-wrap: break-word;">></td></tr>
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
<tr><td style="word-wrap: break-word;">result</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Boolean outcome of the selected comparison between a and b.</td><td style="word-wrap: break-word;">True</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Input range for a and b supports very large integers (including negative values).
- Only the listed operations are supported; selecting an unknown operation yields False.
- On any internal error during evaluation, the node returns False to fail safely.
- The node belongs to the SALT/Logic/Conditions category and is suitable for driving conditional flows.

## Troubleshooting
- If you always get False, verify the 'operation' is one of the supported options (==, !=, <, >, <=, >=).
- Ensure 'a' and 'b' are valid integers within the allowed range if values are coming from upstream nodes.
- If behavior is unexpected around boundaries, double-check the operator choice (e.g., <= vs <).
