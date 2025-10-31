# Accumulation Get Length

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Returns the number of items currently stored in an Accumulation. If the input is not a valid Accumulation object, the node safely returns 0.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/SaltAccumulationGetLengthNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need the count of items collected in an Accumulation, for example to control downstream logic, validate that items have been collected, or to drive iteration counts in a workflow. Typically paired with nodes that build or manipulate Accumulations (e.g., Accumulate, Accumulation Head/Tail, Accumulation To List).

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The Accumulation object whose item count will be measured. Internally expected to be a structure containing a list under the 'accum' key.</td><td style="word-wrap: break-word;">{'accum': ['item1', 'item2', 'item3']}</td></tr>
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
<tr><td style="word-wrap: break-word;">length</td><td style="word-wrap: break-word;">INT</td><td style="word-wrap: break-word;">The number of items in the provided Accumulation. Returns 0 if the input is invalid or empty.</td><td style="word-wrap: break-word;">3</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Graceful fallback**: If the input is not a valid Accumulation or lacks the expected structure, the node returns 0 instead of raising an error.
- **Type expectation**: The Accumulation should be produced by compatible nodes (e.g., Accumulate) to ensure correct structure.
- **Category**: SALT/Logic/Lists

## Troubleshooting
- **Always returning 0**: Ensure the input is a valid Accumulation produced by compatible nodes and that it contains an 'accum' list.
- **Unexpected small count**: Verify that upstream steps actually appended items to the Accumulation before this node runs.
- **Downstream type mismatch**: The output is an INT; if another node expects a different type (e.g., STRING), add a conversion step.
