# PDB Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Merges up to five PDB inputs into a single PDB dictionary. If enabled, it automatically prefixes keys to avoid collisions (e.g., PDB1_, PDB2_). When collisions occur without prefixing, the node will assign numeric suffixes to make keys unique.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have multiple PDB dictionaries from different branches in your workflow and need to consolidate them into one combined PDB output for downstream processing (e.g., conversion, chain extraction, saving/export). Connect any subset of the five available PDB inputs and optionally enable prefixing to preserve key uniqueness and provenance.

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
<tr><td style="word-wrap: break-word;">pdb_1</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">First PDB dictionary to include. Keys are PDB IDs (or chain/spec identifiers), values are PDB file contents as strings.</td><td style="word-wrap: break-word;">{'design_A': 'ATOM ... END'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_2</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Second PDB dictionary to include.</td><td style="word-wrap: break-word;">{'template_B': 'ATOM ... END'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_3</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Third PDB dictionary to include.</td><td style="word-wrap: break-word;">{'candidate_1': 'ATOM ... END'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_4</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fourth PDB dictionary to include.</td><td style="word-wrap: break-word;">{'candidate_2': 'ATOM ... END'}</td></tr>
<tr><td style="word-wrap: break-word;">pdb_5</td><td>False</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Fifth PDB dictionary to include.</td><td style="word-wrap: break-word;">{'ref_structure': 'ATOM ... END'}</td></tr>
<tr><td style="word-wrap: break-word;">prefix_chains</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When true, prefixes keys from each input to avoid collisions (PDB1_, PDB2_, etc.). If disabled and a key collision occurs, numeric suffixes are appended to create unique keys.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A single merged dictionary of all provided PDB entries with unique keys.</td><td style="word-wrap: break-word;">{'PDB1_design_A': 'ATOM ... END', 'PDB2_template_B': 'ATOM ... END'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **At least one input is required**: The node raises an error if no PDB inputs are connected or if all are empty.
- **Input type**: Each pdb_i must be a dictionary mapping IDs to PDB strings. Non-dictionary inputs are ignored.
- **Key collisions**: With prefix_chains=false, duplicate keys are made unique by adding numeric suffixes and a warning may be logged. Enable prefixing to preserve source provenance and avoid collisions.
- **No content validation**: The node does not validate PDB string format; downstream nodes may fail on invalid content.
- **Fixed number of ports**: Supports up to five PDB inputs.
- **Order matters for prefixes**: When prefix_chains is enabled, prefixes correspond to the input index (PDB1_, PDB2_, ...).

## Troubleshooting
- **Error: 'At least one PDB input is required'**: Connect at least one non-empty PDB input.
- **Unexpected overwriting of entries**: Enable prefix_chains to avoid key collisions, or ensure unique keys across inputs.
- **Downstream node errors about invalid PDB**: Check that your PDB strings are complete and properly formatted; this node does not fix or validate PDB content.
- **Missing entries in output**: Ensure each pdb_i is a non-empty dict. Empty strings or non-dict values are skipped.
- **Ambiguous provenance after combining**: Turn on prefix_chains to track which input a given entry originated from (PDB1_, PDB2_, etc.).
