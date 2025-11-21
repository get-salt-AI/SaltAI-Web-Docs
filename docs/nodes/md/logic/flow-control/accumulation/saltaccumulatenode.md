# Accumulate

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds up an ordered collection across steps by appending incoming values to an accumulation. It gracefully handles missing values: if the incoming value is None, it will not be added and the current accumulation is passed through or initialized empty.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../../images/previews/logic/flow-control/accumulation/saltaccumulatenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to collect a sequence of values over time or across branches (e.g., gathering prompts, parameters, IDs, or partial results). Start a new accumulation by leaving the accumulation input unconnected, then chain multiple Accumulate nodes by feeding the prior output back into the accumulation input. The resulting accumulation can be consumed by other list/accumulation utility nodes.

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
<tr><td style="word-wrap: break-word;">to_add</td><td>True</td><td style="word-wrap: break-word;">WILDCARD</td><td style="word-wrap: break-word;">The value to append to the accumulation. Accepts any type and preserves insertion order. If None, it is skipped and the accumulation is passed through unchanged or initialized empty.</td><td style="word-wrap: break-word;">hello world</td></tr>
<tr><td style="word-wrap: break-word;">accumulation</td><td>False</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">An existing accumulation object from a previous step. If omitted, a new accumulation starts. If a non-ACCUMULATION value is provided, it is treated as the first element before appending to_add.</td><td style="word-wrap: break-word;">{'accum': [1, 2, 3]}</td></tr>
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
<tr><td style="word-wrap: break-word;">accumulation</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The updated accumulation object containing all collected items under the key 'accum' as a list.</td><td style="word-wrap: break-word;">{'accum': ['a', 'b', 'c']}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Skip on None**: If to_add is None, nothing is appended. The node returns the current accumulation (or an empty one if none exists).
- **Structure**: The ACCUMULATION output is a dictionary-like object with a single key 'accum' that holds a list. Do not manually alter its structure.
- **Mixed types supported**: You can accumulate heterogeneous item types (strings, numbers, objects). Ensure downstream nodes can handle this.
- **Order preserved**: Items are appended in the order the node is executed, preserving sequence.
- **Flexible initialization**: If accumulation is provided as a non-ACCUMULATION value, it becomes the first list element before appending to_add.
- **Error fallback**: On internal errors, the node returns an empty accumulation: {"accum": []}.

## Troubleshooting
- **Nothing gets added**: Check if to_add is None due to earlier logic. Only non-None values are appended.
- **Accumulation unexpectedly empty**: Ensure the first call either supplies a valid accumulation or a non-None to_add. Also verify no upstream errors occurred.
- **Downstream type errors**: If other nodes expect uniform item types, ensure you are not mixing incompatible types in the accumulation.
- **Wrong input wired**: Make sure to pass the ACCUMULATION output object into the accumulation input, not just the raw list or a single item.
- **Resetting the list**: To start a new accumulation, leave the accumulation input unconnected or explicitly break the feedback connection at the desired point.
