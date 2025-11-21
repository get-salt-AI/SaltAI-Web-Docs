# Boltz List Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines multiple Boltz items (sequences, constraints, templates, properties) into a single ordered list. Accepts up to 50 inputs, each of which can be a single item or a list of items; lists are flattened into one combined list.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzlistcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to gather outputs from one or more Boltz builder nodes into a single list before constructing a Boltz YAML configuration. Typical workflow: build sequences/constraints/templates/properties, feed them into this node (chained inputs appear as you connect), then connect its output to the Boltz YAML Combiner.

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
<tr><td style="word-wrap: break-word;">input_1</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">First Boltz item or list of items to include. Can be a sequence, constraint, template, property, or a list of such items.</td><td style="word-wrap: break-word;">[{'protein': {'id': 'A', 'sequence': 'MKT...', '_sequence_name': 'seqA', 'msa': 'empty'}}]</td></tr>
<tr><td style="word-wrap: break-word;">input_2</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Second Boltz item or list of items. This input becomes available after connecting input_1.</td><td style="word-wrap: break-word;">[{'ligand': {'id': 'L', 'smiles': 'CCO'}}]</td></tr>
<tr><td style="word-wrap: break-word;">input_3</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Third Boltz item or list of items. Becomes available after connecting input_2.</td><td style="word-wrap: break-word;">[{'pocket': {'binder': 'L', 'contacts': [['A', 10], ['A', 25]]}}]</td></tr>
<tr><td style="word-wrap: break-word;">input_4</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional additional input. Up to input_50 are supported, each appearing after the previous is connected.</td><td style="word-wrap: break-word;">[{'pdb': '<PDB_FILE_CONTENT>'}]</td></tr>
<tr><td style="word-wrap: break-word;">input_5..input_50</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Further optional inputs. Each can be a single Boltz item or a list; lists are flattened into the final combined list.</td><td style="word-wrap: break-word;">[{'affinity': {'binder': 'L'}}]</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_list</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A single flattened list containing all provided Boltz items, in the order of inputs.</td><td style="word-wrap: break-word;">[{'protein': {'id': 'A', 'sequence': 'MKT...', '_sequence_name': 'seqA', 'msa': 'empty'}}, {'ligand': {'id': 'L', 'smiles': 'CCO'}}, {'pocket': {'binder': 'L', 'contacts': [['A', 10], ['A', 25]]}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **At least one input is required**: If no inputs are connected or all are null, the node raises an error.
- **Flattening behavior**: If an input is a list, its items are appended individually; single inputs are appended as-is.
- **Dynamic inputs**: Inputs input_2 through input_50 become available only after the previous input is connected (chained reveal).
- **Ordering preserved**: Items are combined in ascending input order (input_1 first, then input_2, etc.).
- **No validation or deduplication**: The node does not validate content types beyond accepting '*', nor does it remove duplicates or enforce schemas.
- **Maximum inputs**: Up to 50 inputs are supported.

## Troubleshooting
- **Error: 'At least one input is required'**: Connect at least one valid item to input_1 (or provide a non-empty list).
- **Unexpected nesting in output**: If you see nested lists, verify that each provided input is either a single item or a flat list; avoid wrapping lists within lists.
- **Missing subsequent inputs**: input_2 (and beyond) appear only after connecting the previous input. Connect input_1 first to reveal input_2, and so on.
- **Type issues downstream**: If a downstream node expects a list of a specific Boltz item type, ensure you pass only those items here or split by multiple combiners.
