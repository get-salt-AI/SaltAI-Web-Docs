# Batch PDB

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines multiple PDB inputs into a single batch. Each input can be a single PDB or an existing batch (dictionary) of PDBs. The node merges all inputs into one dictionary keyed by PDB IDs and enforces that all IDs are unique.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/batchpdbnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to aggregate several protein structures before downstream steps that accept batched PDBs (for example, analysis, scoring, or visualization pipelines). Start by connecting pdb_1, then additional inputs (pdb_2, pdb_3, …) become available sequentially. It is suitable for merging outputs from multiple sources or for grouping structures for batch processing.

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
<tr><td style="word-wrap: break-word;">pdb_1</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">First PDB to add to the batch. Can be either a single PDB dict or a batch dict. Keys are PDB IDs and values are PDB content strings.</td><td style="word-wrap: break-word;">{"protA": "ATOM ... END"}</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Merged batch of PDBs as a dictionary {pdb_id: pdb_content}. Guaranteed to have unique PDB IDs.</td><td style="word-wrap: break-word;">{"protA": "ATOM ... END", "protB": "ATOM ... END"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- IDs must be unique across all merged inputs. Duplicate IDs will cause the node to raise an error.
- Each input accepts either a single PDB (e.g., {"id": "pdb_string"}) or a batch (multiple key-value pairs). The node flattens and merges them.
- Inputs beyond pdb_1 are hidden until the previous input is connected, guiding you to add PDBs sequentially.
- Output preserves only one flat dictionary; nested batches are merged into a single level keyed by PDB IDs.

## Troubleshooting
- Duplicate PDB IDs error: Ensure that all inputs use distinct PDB IDs. If two inputs contain the same key, rename one before merging.
- Cannot access pdb_2 or later: Connect pdb_1 first; additional inputs become available only after the previous one is set.
- Unexpected content type: Inputs must be PDB-typed dictionaries mapping IDs to PDB strings. Verify upstream nodes provide the correct format.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/batchpdbnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

