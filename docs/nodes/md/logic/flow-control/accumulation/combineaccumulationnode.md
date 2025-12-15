# Combine Accumulation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts an accumulation object into a single combined list. It expects an accumulation structure containing a key named "accum" and outputs that list; if the structure is missing or invalid, it returns an empty list.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/combineaccumulationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after one or more nodes that build up an accumulation across steps or branches. It finalizes the accumulation by producing a single list you can pass to downstream nodes for batched processing, saving, or further aggregation.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An accumulation object produced by upstream nodes. Must be a dictionary-like object containing the key "accum" that maps to a list of collected items.</td><td style="word-wrap: break-word;">{"accum": ["seq1", "seq2", "seq3"]}</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_list</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The combined list extracted from the accumulation (the value of accumulation["accum"]). If the input is invalid or missing, this will be an empty list.</td><td style="word-wrap: break-word;">["seq1", "seq2", "seq3"]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- The input must be a mapping with the key "accum" pointing to a list. Any other structure results in an empty list output.
- Output type is generic (*) and can contain any item types collected upstream (e.g., strings, dicts, file references).
- This node does not modify, sort, or deduplicate the accumulated items; it returns the list as provided.
- If the accumulation is missing or malformed, the node quietly returns an empty list and logs an error internally.

## Troubleshooting
- Output is an empty list: Ensure the input is a dict-like object with the key "accum" and that it contains the collected items.
- Downstream node rejects the output type: Verify downstream compatibility with a generic list and confirm the items inside the list are of the expected type.
- Unexpected ordering of items: Check upstream accumulation logic; this node preserves the order it receives and does not reorder items.
- Receiving type errors upstream: Confirm the producer nodes are correctly constructing the accumulation object with the "accum" key as a list.
