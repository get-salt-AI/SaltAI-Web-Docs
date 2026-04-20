# Accumulation Get Length

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node reads an ACCUMULATION object and outputs how many elements it currently contains. It looks for the internal list under the "accum" key and returns its length as an integer. If the structure is invalid or an error occurs, it safely falls back to 0 instead of failing the workflow.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/logic/saltaccumulationgetlengthnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to know how many items have been collected in an accumulation to drive control flow or validation. A typical pattern is to first build or transform an accumulation using nodes like "Accumulate", "Accumulation Head", "Accumulation Tail", or "List to Accumulation", then feed their ACCUMULATION output into "Accumulation Get Length". The resulting integer can be routed into comparison or conditional logic nodes (for example to trigger processing when a batch reaches N items), used as a guard before accessing elements with "Accumulation Get Item", or surfaced for monitoring and reporting. In list-processing pipelines, place this node after your accumulation-building steps and before index-based operations or final aggregation. It complements other accumulation utilities such as "Accumulation Get Item", "Accumulation Set Item", and "Accumulation to List".

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The accumulation structure whose length should be measured. It must be an ACCUMULATION object produced by Salt list/accumulation nodes, typically a dict-like structure with an "accum" key containing a list of collected items. If the value is not a dict with an "accum" list, the node does not raise an error but returns a length of 0.</td><td style="word-wrap: break-word;">{'accum': ['email_thread_1001', 'email_thread_1002', 'email_thread_1003', 'email_thread_1004']}</td></tr>
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
<tr><td style="word-wrap: break-word;">length</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The number of elements currently stored under the "accum" key in the ACCUMULATION input. This always returns an integer representing the size of the internal list. If the input is malformed or an internal error occurs, the node outputs 0 rather than stopping the pipeline.</td><td style="word-wrap: break-word;">4</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: Length is computed via a direct size check on the internal list, so it is very fast and suitable even for large accumulations.
- **Limitations**: If the input is not a valid ACCUMULATION (for example, missing the "accum" key or not being dict-like), the node silently returns 0 instead of signaling a hard error, which can hide upstream wiring or type issues.
- **Behavior**: Any exception during length retrieval is caught and logged internally, and the node falls back to returning 0 to keep the workflow running.
- **Behavior**: The node is read-only and never mutates the ACCUMULATION input; it only inspects it to compute the length.

## Troubleshooting
- **Observed length is always 0**: Check that the input is connected to an ACCUMULATION-producing node such as "Accumulate" or "List to Accumulation", not directly to a plain list or other type, and confirm items are actually being added upstream.
- **Length smaller than expected**: Review upstream nodes like "Accumulation Head" or "Accumulation Tail" that may be slicing or trimming the accumulation before it reaches this node, reducing the number of items.
- **Index errors in downstream nodes despite using length**: When using this length with nodes like "Accumulation Get Item", ensure indices range from 0 to length - 1. Using length itself as an index will be out of range and cause downstream errors.
