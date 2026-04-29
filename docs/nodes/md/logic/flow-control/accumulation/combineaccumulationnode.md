# Combine Accumulation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node takes an accumulation object and extracts the list stored under its "accum" key. It returns that list as a generic output so it can be used by many different downstream nodes. If the input does not match the expected structure or an error occurs, the node safely returns an empty list instead of failing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/combineaccumulationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have one or more upstream steps that progressively build an accumulation (ACCUMULATION type) and you are ready to finalize it into a simple list for further processing. Typical upstream nodes are custom accumulator or batching nodes that collect items such as sequences, PDB structures, alignment records, or generic data objects into an `accum` field. Connect their ACCUMULATION output to this node to obtain a plain list, which you can then feed into nodes that expect iterable data, such as batch processors, exporters, summarizers, or visualization utilities. In biotech-oriented workflows, this is often the final step after repeatedly appending structures or sequences to an accumulation object and before running downstream analysis or saving all items together.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An accumulation object that should be a dictionary-like structure containing a key named "accum" whose value is the list of collected items. If the input is not a dict or does not contain the "accum" key, the node will interpret it as invalid and return an empty list. The list can hold any item type (strings, dicts, numbers, or domain-specific biotech objects), but downstream nodes must know how to handle those items.</td><td style="word-wrap: break-word;">{ "accum": [ { "pdb_id": "1ABC", "chain": "A" }, { "pdb_id": "2XYZ", "chain": "B" } ] }</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_list</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">The list extracted from the input accumulation object, specifically the value of its "accum" key. If the input is malformed or lacks the expected key, this output will be an empty list. The structure is a standard list that downstream nodes can iterate over, batch, filter, or transform as needed.</td><td style="word-wrap: break-word;">[ { "pdb_id": "1ABC", "chain": "A" }, { "pdb_id": "2XYZ", "chain": "B" } ]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs only a simple type check and dictionary lookup, so it adds negligible overhead even for large lists; any heavy computation should remain in other nodes.
- **Limitations**: The node does not attempt to repair or infer incorrect structures; if the input is not a dict with an "accum" key, it will just return an empty list.
- **Behavior**: All internal errors during conversion are caught and logged, and the node returns an empty list instead of raising an exception, which keeps the workflow running but may hide structural issues in the input.
- **Behavior**: The output type is generic (`*`), so the node does not validate or enforce any schema on the items in the list; ensure that what you accumulate is compatible with your chosen downstream nodes.

## Troubleshooting
- **Empty output when items were expected**: If `combined_list` is an empty list, verify that the upstream node is creating an ACCUMULATION object shaped like `{ "accum": [...] }` and that the connection into this node is correctly configured.
- **Downstream node reports type or schema errors**: Inspect the contents of the `accum` list in your accumulation object to confirm that each item has the fields and types required by the next node (for example, required keys in dict items).
- **Logs mention 'Error converting accumulation to list'**: This indicates the input was not in the expected format or another unexpected error occurred; check the actual structure of the `accumulation` input and ensure it is a dict with a valid `accum` list.
