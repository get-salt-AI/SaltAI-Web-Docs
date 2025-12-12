# Combine Accumulation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Extracts and returns the accumulated items from an accumulation object as a plain list. If the input does not match the expected format, it safely returns an empty list.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/combineaccumulationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after any node that builds up an accumulation (e.g., iterative/batch collectors) to convert the accumulated results into a simple list that downstream nodes can consume. Connect the output wherever a standard list of items is needed.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An accumulation object produced by an upstream collector node. It should be a dictionary containing the key 'accum' mapped to a list of collected items.</td><td style="word-wrap: break-word;">{"accum": ["seq1", "seq2", "seq3"]}</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_list</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A plain list extracted from the accumulation input. If the input is invalid or missing, this will be an empty list.</td><td style="word-wrap: break-word;">["seq1", "seq2", "seq3"]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: The node expects a dictionary-like object with an 'accum' key containing a list. Any other structure results in an empty list output.
- **Graceful fallback**: On errors or malformed inputs, the node returns an empty list instead of failing the workflow.
- **Type-agnostic output**: The output type is a generic list (*). Items are passed through unchanged.
- **Single responsibility**: This node does not modify items; it only unwraps the accumulation into a list.

## Troubleshooting
- **Empty output list**: Ensure the input is a valid accumulation object like {"accum": [...]} and that the upstream node producing it is correctly connected.
- **Unexpected item types**: The node passes items through unchanged. Verify the upstream collector produced the expected item types.
- **Downstream type errors**: Some nodes may expect a specific data type rather than a generic list. Confirm the downstream node accepts a list and the item types inside it.
