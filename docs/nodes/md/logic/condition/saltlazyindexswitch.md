# Lazy Index Switch

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Selects and forwards exactly one of up to ten inputs based on an integer index (0–9). Inputs are lazily evaluated: only the value corresponding to the selected index is requested/computed. This enables efficient branching without computing unused inputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/logic/condition/saltlazyindexswitch.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use when you need to route one of several possible values downstream based on a numeric selector. Typical in workflows where you predefine multiple alternatives (e.g., different prompts, images, or parameters) and choose one dynamically at run time. Connect any data type to value0..value9; set the index to pick which one is emitted.

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
<tr><td style="word-wrap: break-word;">index</td><td>True</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">Zero-based selector for which input value to forward. Must be between 0 and 9.</td><td style="word-wrap: break-word;">3</td></tr>
<tr><td style="word-wrap: break-word;">value0</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 0. Lazily evaluated; only needed if index is 0.</td><td style="word-wrap: break-word;">First option</td></tr>
<tr><td style="word-wrap: break-word;">value1</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 1. Lazily evaluated; only needed if index is 1.</td><td style="word-wrap: break-word;">Second option</td></tr>
<tr><td style="word-wrap: break-word;">value2</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 2. Lazily evaluated; only needed if index is 2.</td><td style="word-wrap: break-word;">Third option</td></tr>
<tr><td style="word-wrap: break-word;">value3</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 3. Lazily evaluated; only needed if index is 3.</td><td style="word-wrap: break-word;">Fourth option</td></tr>
<tr><td style="word-wrap: break-word;">value4</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 4. Lazily evaluated; only needed if index is 4.</td><td style="word-wrap: break-word;">Fifth option</td></tr>
<tr><td style="word-wrap: break-word;">value5</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 5. Lazily evaluated; only needed if index is 5.</td><td style="word-wrap: break-word;">Sixth option</td></tr>
<tr><td style="word-wrap: break-word;">value6</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 6. Lazily evaluated; only needed if index is 6.</td><td style="word-wrap: break-word;">Seventh option</td></tr>
<tr><td style="word-wrap: break-word;">value7</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 7. Lazily evaluated; only needed if index is 7.</td><td style="word-wrap: break-word;">Eighth option</td></tr>
<tr><td style="word-wrap: break-word;">value8</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 8. Lazily evaluated; only needed if index is 8.</td><td style="word-wrap: break-word;">Ninth option</td></tr>
<tr><td style="word-wrap: break-word;">value9</td><td>False</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">Candidate value for index 9. Lazily evaluated; only needed if index is 9.</td><td style="word-wrap: break-word;">Tenth option</td></tr>
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
<tr><td style="word-wrap: break-word;">value</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The selected value corresponding to the provided index.</td><td style="word-wrap: break-word;">Fourth option</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Index range**: Only indices 0–9 are supported.
- **Lazy evaluation**: The node requests/computes only the input matching the selected index. Unselected inputs are not evaluated.
- **Provide the selected input**: If the chosen index points to an unconnected/missing value input, the system will request it; if still missing at execution, the node outputs null.
- **Type-agnostic**: The node passes through any type (WILDCARD). Ensure downstream nodes accept the selected value’s type.
- **No transformation**: The node does not modify the selected value; it only routes it.

## Troubleshooting
- **Nothing outputs**: Verify that the value input matching the current index is connected. Connect valueN for the chosen index or change the index to one that is connected.
- **Type mismatch downstream**: Ensure the downstream node can handle the data type of the selected value (e.g., IMAGE vs. STRING).
- **Unexpected null output**: This occurs if the selected value input is missing or failed to load. Connect the input or change the index.
- **Cannot set index beyond 9**: The node is limited to 0–9. Add another node or refactor branching if you need more options.
