# Lazy Index Switch

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Selects and forwards exactly one of up to 10 candidate inputs based on an integer index, without evaluating the others. Inputs are lazily requested only when the chosen index requires them, helping avoid unnecessary computation. Returns the selected input value unchanged and preserves its data type.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltlazyindexswitch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to route a single value from multiple alternatives by index, especially when upstream branches are expensive to compute. Set the index to the desired branch (0–9); only that input will be resolved and passed downstream. Ideal for A/B testing, branching workflows, or selecting among preconfigured options without triggering all upstream work.

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
<tr><td style="word-wrap: break-word;">index</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based index of the input to forward. Must be between 0 and 9.</td><td style="word-wrap: break-word;">2</td></tr>
<tr><td style="word-wrap: break-word;">value0</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value at index 0. Lazily evaluated. Can be any supported type (e.g., IMAGE, MASK, STRING, NUMBER, MODEL, etc.).</td><td style="word-wrap: break-word;">Any supported type value</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value at index 1. Lazily evaluated.</td><td style="word-wrap: break-word;">Any supported type value</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value at index 2. Lazily evaluated.</td><td style="word-wrap: break-word;">Any supported type value</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value at index 3. Lazily evaluated.</td><td style="word-wrap: break-word;">Any supported type value</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value at index 4. Lazily evaluated.</td><td style="word-wrap: break-word;">Any supported type value</td></tr>
<tr><td style="word-wrap: break-word;">value5</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value at index 5. Lazily evaluated.</td><td style="word-wrap: break-word;">Any supported type value</td></tr>
<tr><td style="word-wrap: break-word;">value6</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value at index 6. Lazily evaluated.</td><td style="word-wrap: break-word;">Any supported type value</td></tr>
<tr><td style="word-wrap: break-word;">value7</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value at index 7. Lazily evaluated.</td><td style="word-wrap: break-word;">Any supported type value</td></tr>
<tr><td style="word-wrap: break-word;">value8</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value at index 8. Lazily evaluated.</td><td style="word-wrap: break-word;">Any supported type value</td></tr>
<tr><td style="word-wrap: break-word;">value9</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value at index 9. Lazily evaluated.</td><td style="word-wrap: break-word;">Any supported type value</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value from the selected index, forwarded unchanged. Type matches the chosen input.</td><td style="word-wrap: break-word;">Same type and content as the selected input (e.g., an IMAGE if an IMAGE input was selected)</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Index must point to an input that is provided. Only value0 is required; value1–value9 are optional.
- Only the selected input is evaluated/resolved; unselected inputs remain unevaluated.
- If the selected input is missing or None, the node will output None.
- The output type is the same as the selected input and may vary between runs based on the index.
- Valid index range is 0–9; values outside this range are invalid.

## Troubleshooting
- Output is None: Ensure the index corresponds to a connected/provided valueN input.
- Downstream type mismatch: Set the index to a branch whose type matches what downstream nodes expect.
- Unexpected computation happening: Verify the index is fixed and not dynamically changing; only the chosen branch should be evaluated.
- Index out of range error: Keep index between 0 and 9 and connect the corresponding valueN input.
