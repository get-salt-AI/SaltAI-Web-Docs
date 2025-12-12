# PDB Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Merges multiple PDB inputs into a single dictionary of structures. Accepts up to five PDB dictionaries and optionally prefixes keys to avoid collisions. If duplicate keys occur, the node auto-resolves by appending a numeric suffix.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to consolidate several PDB outputs (e.g., from multiple loaders or processing branches) into one combined PDB collection for downstream steps such as conversion, filtering, or visualization. Connect any subset of pdb_1 to pdb_5 and enable prefix_chains to systematically avoid key conflicts.

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
<tr><td style="word-wrap: break-word;">pdb_1</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">First PDB dictionary to include in the merge. Each PDB input must be a dict of {pdb_id: pdb_string}.</td><td style="word-wrap: break-word;">{'modelA': 'ATOM ...\\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_2</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Second PDB dictionary to include.</td><td style="word-wrap: break-word;">{'modelB': 'ATOM ...\\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_3</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Third PDB dictionary to include.</td><td style="word-wrap: break-word;">{'complex1': 'ATOM ...\\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_4</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fourth PDB dictionary to include.</td><td style="word-wrap: break-word;">{'complex2': 'ATOM ...\\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_5</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fifth PDB dictionary to include.</td><td style="word-wrap: break-word;">{'template': 'ATOM ...\\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">prefix_chains</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, automatically prefixes keys from each input (PDB1_, PDB2_, etc.) to avoid ID collisions.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A single merged dictionary of all provided PDBs, keyed by (possibly prefixed) IDs.</td><td style="word-wrap: break-word;">{'PDB1_modelA': 'ATOM ...\\nEND', 'PDB2_modelB': 'ATOM ...\\nEND'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- At least one PDB input (pdb_1–pdb_5) must be connected; otherwise the node raises an error.
- Each PDB input must be a dictionary mapping IDs to PDB text content (e.g., {"my_id": "ATOM ... END"}).
- When keys collide and prefix_chains is disabled, duplicates are resolved by appending _1, _2, etc., and a warning is logged.
- Enable prefix_chains to systematically avoid collisions by adding PDB1_, PDB2_, etc. to incoming keys.
- This node merges up to five inputs; if you need more, combine upstream or chain multiple combiners.

## Troubleshooting
- Error: "At least one PDB input is required" – Connect at least one pdb_i input with a non-empty PDB dictionary.
- Unexpected overwritten or duplicate keys – Enable prefix_chains to add unique prefixes, or ensure source IDs are unique.
- Invalid input type – Ensure each connected pdb_i is a dictionary of {id: pdb_string}, not a raw string.
- Empty result – Verify that connected inputs are non-empty dictionaries and that upstream nodes produced valid PDB content.
