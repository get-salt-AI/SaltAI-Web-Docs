# Batch PDB

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Aggregates multiple PDB inputs into a single batch. Each input can be a single PDB dictionary or an existing batch; the node flattens and merges them into one dictionary. It enforces unique PDB IDs across all inputs and raises an error if duplicates are found.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/batchpdbnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to merge multiple PDB structures into one consolidated batch for downstream processing (e.g., visualization, format conversion, chain extraction). Start by connecting at least one PDB-producing node (such as Load PDB), then progressively reveal and connect additional pdb_i inputs as needed. Ideal for workflows where multiple structures must be processed together.

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
<tr><td style="word-wrap: break-word;">pdb_1</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">First PDB input. Accepts a dictionary mapping {pdb_id: pdb_content}. Can also be a batch (multiple entries).</td><td style="word-wrap: break-word;">{'my_protein': 'ATOM ... END'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_2</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Second PDB input. Revealed after pdb_1 is connected. Accepts a single or batched PDB dictionary.</td><td style="word-wrap: break-word;">{'another_protein': 'ATOM ... END'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_3</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Third PDB input. Revealed after pdb_2 is connected.</td><td style="word-wrap: break-word;">{'third_protein': 'ATOM ... END'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_4</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fourth PDB input. Revealed after pdb_3 is connected.</td><td style="word-wrap: break-word;">{'fourth_protein': 'ATOM ... END'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_5</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fifth PDB input. Revealed after pdb_4 is connected.</td><td style="word-wrap: break-word;">{'fifth_protein': 'ATOM ... END'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_6..pdb_31</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Additional PDB inputs up to pdb_31. Each becomes available after the previous one is connected. Each accepts a single or batched PDB dictionary.</td><td style="word-wrap: break-word;">{'protX': 'ATOM ... END'}</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Merged batch of PDBs represented as a dictionary {pdb_id: pdb_content}.</td><td style="word-wrap: break-word;">{'my_protein': 'ATOM ... END', 'another_protein': 'ATOM ... END'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- IDs must be unique across all connected inputs; duplicate pdb_id values cause a validation error.
- You can connect either single PDB dictionaries or already-batched PDB dictionaries; the node flattens and merges them.
- Inputs are progressively revealed: pdb_2 appears after connecting pdb_1, pdb_3 after pdb_2, and so on up to pdb_31.
- Provide inputs using compatible PDB outputs (e.g., from Load PDB or other nodes that output type PDB).
- The output is a single PDB dictionary suitable for downstream nodes that accept batched PDBs.

## Troubleshooting
- Duplicate PDB ID error: Ensure each connected input uses unique keys in its {pdb_id: pdb_content} dictionary.
- No output produced: Verify at least pdb_1 is connected and contains valid PDB content.
- Type mismatch: Confirm each input is of type PDB (a dictionary mapping IDs to PDB strings), not a plain string.
- Unexpectedly missing inputs: Additional pdb_i fields appear only after the previous one is connected; connect sequentially to reveal more slots.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/batchpdbnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

