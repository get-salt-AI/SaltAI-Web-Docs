# Combine Accumulation

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node unwraps an ACCUMULATION object and outputs the underlying accumulated values as a standard list. It is designed as a terminal step for workflows that build up collections of items across iterations. When the input is invalid or does not contain accumulated data, it safely returns an empty list instead of raising an error.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/utilities/combineaccumulationnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use the Combine Accumulation node at the end of a pipeline that has been building an ACCUMULATION object across multiple steps or iterations. Typical use cases include aggregating multiple protein structures, sequence records, or metadata entries created in a loop-like or batching workflow, then converting that collection into a plain list for further filtering, analysis, or export. Upstream, this node expects any node that outputs an `ACCUMULATION` type (for example, a custom accumulator, loop collector, or batch generator specific to your biotech workflows). Downstream, you can connect nodes that operate on generic lists (`*`), such as nodes that iterate through items, apply transformations to each element, or save multiple outputs (e.g., storing multiple PDBs, FASTA entries, or result records). This node does not transform the individual elements; it only exposes them as a list, so design downstream nodes with the exact element structure in mind.

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
<tr><td style="word-wrap: break-word;">accumulation</td><td>True</td><td style="word-wrap: break-word;">ACCUMULATION</td><td style="word-wrap: break-word;">The accumulation container holding the collected items. Internally, it is expected to be a dictionary-like object with an `accum` key that maps to a list of accumulated entries. Each entry can be any serializable object used in your biotech workflow (e.g., dictionaries with PDB info, sequence strings, or configuration records). If this structure is missing or malformed, the node returns an empty list.</td><td style="word-wrap: break-word;">{'accum': [{'pdb_id': '1ABC', 'chain': 'A', 'sequence': 'MSEQNNTEMTFQIQRIYTKDISFEAPNAPHVFQKDW', 'metadata': {'source': 'RCSB', 'resolution': 1.8, 'organism': 'Homo sapiens'}}, {'pdb_id': '2XYZ', 'chain': 'B', 'sequence': 'GAMGSNTYEEQLDALYEKLGLDGYKLER', 'metadata': {'source': 'local', 'notes': 'engineered mutant', 'date_added': '2026-03-15'}}]}</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_list</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A plain list extracted from the `accum` field of the ACCUMULATION object. The structure of each list element is exactly what was stored in the accumulator (e.g., dictionaries describing PDB entries, sequence objects, or metadata records). If the input is invalid or does not contain `accum`, this output is an empty list, allowing downstream nodes to handle the no-data case explicitly.</td><td style="word-wrap: break-word;">[{'pdb_id': '1ABC', 'chain': 'A', 'sequence': 'MSEQNNTEMTFQIQRIYTKDISFEAPNAPHVFQKDW', 'metadata': {'source': 'RCSB', 'resolution': 1.8, 'organism': 'Homo sapiens'}}, {'pdb_id': '2XYZ', 'chain': 'B', 'sequence': 'GAMGSNTYEEQLDALYEKLGLDGYKLER', 'metadata': {'source': 'local', 'notes': 'engineered mutant', 'date_added': '2026-03-15'}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node itself performs only a simple extraction operation; performance concerns arise mainly when the `accum` list contains a very large number of elements, which may increase memory usage in downstream processing.
- **Limitations**: The node assumes the ACCUMULATION structure carries an `accum` key. If a custom or non-standard accumulator is used upstream that does not follow this convention, the node will not be able to extract items and will return an empty list.
- **Behavior**: All exceptions during extraction are caught and logged internally; instead of throwing an error, the node returns an empty list. This means upstream structural issues may appear as silently empty outputs if logs are not monitored.
- **Behavior**: The node does not validate or alter the contents of the `accum` list. Any schema or type validation must be performed by downstream nodes, which should be designed to expect the specific element structure being accumulated.

## Troubleshooting
- **Empty output when data expected**: If `combined_list` is empty even though you expect many items, verify that the upstream node actually produces an object with the shape `{ "accum": [...] }` and that the `accum` list is non-empty before it reaches this node.
- **Downstream type or schema errors**: If a downstream node reports unexpected-field or type errors, inspect a single element from `combined_list` to confirm that its keys and value types match what the downstream node expects (e.g., check that required fields like `pdb_id` or `sequence` are present).
- **Intermittent empties in iterative workflows**: In looped or conditional pipelines, some iterations may legitimately produce empty accumulations. If you see occasional empty `combined_list` outputs, confirm whether those iterations were supposed to generate items or if the control logic intentionally skipped accumulation.
- **Debugging hidden exceptions**: Because internal exceptions are logged but not surfaced as hard failures, check the system or service logs for messages similar to "Error converting accumulation to list" whenever you see unexpected empty outputs; this often indicates a malformed ACCUMULATION input.
