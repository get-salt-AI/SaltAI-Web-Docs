# Accumulation Tail

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Returns the tail of an accumulation by removing its first element. If the accumulation has zero or one item, it outputs an empty accumulation. Designed to work safely, returning an empty accumulation on invalid input.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/Logic/Flow Control/Accumulation/SaltAccumulationTailNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use when you need to process all elements of an accumulation except the first, such as in recursive or iterative list processing. Commonly paired with Accumulate (to build the list) and Accumulation Head (to read the first item) for head/tail workflows.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The accumulation object to slice. It represents a sequence collected across steps.</td><td style="word-wrap: break-word;">An ACCUMULATION produced by Accumulate or List to Accumulation</td></tr>
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
<tr><td style="word-wrap: break-word;">tail</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">All items of the input accumulation except the first, preserved as an accumulation object.</td><td style="word-wrap: break-word;">An ACCUMULATION containing the remaining elements after dropping the head</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Empty handling**: If the input accumulation has no items or only one item, the output is an empty accumulation.
- **Robustness**: On invalid input or errors, the node returns an empty accumulation rather than failing.
- **Data shape**: This node expects an ACCUMULATION object. If you have a plain list, convert it using List to Accumulation.
- **Typical pairing**: Often used with Accumulation Head for head/tail decomposition patterns.
- **Order preservation**: Aside from removing the first element, the relative order of the remaining items is preserved.

## Troubleshooting
- **Output is empty unexpectedly**: Ensure the input is a valid ACCUMULATION and contains more than one element. If you're passing a plain list, convert it first using List to Accumulation.
- **Type mismatch errors elsewhere**: Some nodes expect plain lists. If you need a list, follow this node with Accumulation to List.
- **Unexpected None or missing values**: Verify that earlier steps correctly built the accumulation with Accumulate and didn't pass None-only sequences.
