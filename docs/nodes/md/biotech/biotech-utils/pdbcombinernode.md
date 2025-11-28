# PDB Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines up to five PDB inputs (each a dictionary of {pdb_id: pdb_content}) into a single merged PDB dictionary. Optionally prefixes keys to avoid naming collisions across inputs and guarantees unique keys in the combined output.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have multiple PDB dictionaries from different workflow branches and need a single, consolidated PDB input for downstream processing (e.g., conversion, fixing, or chain extraction). Connect any subset of the available pdb_1..pdb_5 inputs; enable prefixing if you want deterministic, non-conflicting keys.

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
<tr><td style="word-wrap: break-word;">pdb_1</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">First PDB dictionary to include in the combination. Keys are PDB IDs and values are PDB file contents as strings.</td><td style="word-wrap: break-word;">{'protein_A': 'ATOM ...\\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_2</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Second PDB dictionary to include in the combination.</td><td style="word-wrap: break-word;">{'protein_B': 'ATOM ...\\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_3</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Third PDB dictionary to include in the combination.</td><td style="word-wrap: break-word;">{'protein_C': 'ATOM ...\\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_4</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fourth PDB dictionary to include in the combination.</td><td style="word-wrap: break-word;">{'protein_D': 'ATOM ...\\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_5</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fifth PDB dictionary to include in the combination.</td><td style="word-wrap: break-word;">{'protein_E': 'ATOM ...\\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">prefix_chains</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, prefixes each input’s keys to avoid collisions (e.g., PDB1_<id>, PDB2_<id>). When false, collisions are auto-resolved by suffixing numeric counters.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A single merged PDB dictionary containing all entries from the provided inputs, with guaranteed unique keys.</td><td style="word-wrap: break-word;">{'PDB1_protein_A': 'ATOM ...\\nEND', 'PDB2_protein_B': 'ATOM ...\\nEND'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Key collisions**: If two inputs share the same key and prefix_chains is false, the node will automatically append numeric suffixes to create unique keys and log a warning.
- **Prefixing behavior**: When prefix_chains is true, the node prepends PDB1_, PDB2_, etc. to the keys based on the input slot to avoid collisions.
- **No structural edits**: This node does not modify the internal PDB content or biological chain identifiers; it only merges key-value pairs from the input dictionaries.
- **At least one input required**: The node raises an error if no PDB inputs are provided or all are empty.

## Troubleshooting
- **Error: 'At least one PDB input is required'**: Connect at least one pdb_i input with a non-empty PDB dictionary.
- **Unexpected renamed keys**: If you see keys with numeric suffixes (e.g., my_pdb_1), either enable prefix_chains for deterministic names or ensure your input keys are unique.
- **Wrong input type**: Ensure each pdb_i is a dictionary of {pdb_id: pdb_string}. Passing a raw PDB string or other types will not be recognized.
- **Missing entries in output**: Verify the connected inputs actually contain entries; empty dictionaries are ignored during merge.
