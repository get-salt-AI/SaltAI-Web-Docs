# PDB Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Merges up to five PDB dictionaries into a single PDB dictionary. It can optionally prefix keys based on the input slot (PDB1_, PDB2_, etc.) to avoid name conflicts, and always guarantees unique keys by adding numeric suffixes if needed. This enables safe consolidation of multiple structure sets without overwriting entries.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have several PDB inputs (each a dictionary mapping identifiers to PDB text strings) and need to combine them into one unified structure for downstream biotech processing, analysis, or visualization. Connect any subset of pdb_1 through pdb_5; enable prefix_chains if you want deterministic, slot-based prefixes for each group of structures.

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
<tr><td style="word-wrap: break-word;">prefix_chains</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, prefixes each key with PDBi_ (based on the input slot index) to avoid key conflicts. If false, duplicate keys are still made unique by appending numeric suffixes (_1, _2, etc.) instead of prefixes.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A single PDB dictionary containing all merged entries from the provided inputs. All keys are guaranteed unique, either via slot-based prefixes or numeric suffixes when collisions occur.</td><td style="word-wrap: break-word;">{"PDB1_pdbA": "<pdb-string-content>", "PDB2_pdbB": "<pdb-string-content>"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **At least one input is required**: If none of pdb_1 through pdb_5 contain valid data, the node raises an error instead of producing an empty result.
- **Key uniqueness handling**: With prefix_chains enabled, keys are prefixed with PDB1_, PDB2_, etc. Without it, duplicate keys are resolved by appending numeric suffixes (_1, _2, ...), rather than overwriting.
- **Expected input format**: Each pdb_i must be a non-empty dictionary mapping an identifier (string) to a PDB text string. Empty dictionaries or plain strings are not considered valid.
- **Non-destructive merging**: All valid entries from connected inputs are preserved; name conflicts are resolved by renaming, not by dropping or overwriting existing entries.

## Troubleshooting
- **Issue 1**: 'At least one PDB input is required' error – Connect at least one non-empty PDB dictionary to one of pdb_1–pdb_5 and confirm the upstream node outputs a PDB-typed dictionary.
- **Issue 2**: Unexpected key collisions or renamed IDs – If keys appear with _1, _2 suffixes, you likely have overlapping identifiers while prefix_chains is disabled. Enable prefix_chains to get deterministic, slot-based prefixes and avoid suffix-based renaming.
- **Issue 3**: Invalid input type or structure – If the node fails or no output appears, verify that each pdb_i is of type PDB and is shaped as {"id": "pdb_string"}. Passing a plain PDB string or an empty structure can make the node behave as if no input was provided.
- **Issue 4**: Missing or empty output – Ensure upstream nodes produced non-empty PDB text strings. Empty PDB strings or entirely empty dictionaries can lead to fewer merged entries or trigger the 'no input' error if everything is empty.
