# PDB Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Merges up to five PDB dictionaries into a single PDB dictionary. Optionally prefixes keys to avoid name conflicts by adding PDB1_, PDB2_, etc. Ensures unique keys even without prefixing by appending numeric suffixes when duplicates occur.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have multiple PDB inputs (each as a dictionary mapping an ID to a PDB string) and need to consolidate them into one structure for downstream steps like extraction, conversion, or visualization. Connect any subset of pdb_1 through pdb_5 and enable prefix_chains if you want guaranteed unique IDs derived from each input slot.

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
<tr><td style="word-wrap: break-word;">pdb_1</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">First PDB dictionary to merge. Keys are identifiers; values are PDB content strings.</td><td style="word-wrap: break-word;">{"pdbA": "<pdb-string-content>"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_2</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Second PDB dictionary to merge.</td><td style="word-wrap: break-word;">{"pdbB": "<pdb-string-content>"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_3</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Third PDB dictionary to merge.</td><td style="word-wrap: break-word;">{"pdbC": "<pdb-string-content>"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_4</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fourth PDB dictionary to merge.</td><td style="word-wrap: break-word;">{"pdbD": "<pdb-string-content>"}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_5</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fifth PDB dictionary to merge.</td><td style="word-wrap: break-word;">{"pdbE": "<pdb-string-content>"}</td></tr>
<tr><td style="word-wrap: break-word;">prefix_chains</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, prefixes each key with PDBi_ (based on the input slot) to avoid key conflicts. If false, duplicates are resolved by appending _1, _2, etc.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A single dictionary containing all merged PDB entries with unique keys.</td><td style="word-wrap: break-word;">{"PDB1_pdbA": "<pdb-string-content>", "PDB2_pdbB": "<pdb-string-content>"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **At least one input must be provided**: If none of pdb_1 to pdb_5 are connected or contain data, the node raises an error.
- **Key uniqueness handling**: With prefix_chains enabled, keys are prefixed (PDB1_, PDB2_, etc.). Without it, duplicate keys are made unique by appending numeric suffixes (_1, _2, ...).
- **Input format**: Each PDB input must be a non-empty dictionary mapping an identifier to a PDB text string.
- **Merging behavior**: All entries from provided inputs are included; conflicts are resolved rather than overwritten.

## Troubleshooting
- **Error: 'At least one PDB input is required'**: Connect at least one valid PDB dictionary to pdb_1–pdb_5.
- **Unexpected key collisions**: Enable prefix_chains to systematically avoid collisions by slot-based prefixes.
- **Invalid input type**: Ensure each pdb_i is a dictionary {id: pdb_string}. Passing plain strings or empty values will be ignored and can lead to errors if nothing valid remains.
- **Missing outputs**: Verify your inputs are non-empty and contain valid PDB text. Empty strings are skipped.
