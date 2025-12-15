# PDB To CIF

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts one or more protein structures from PDB format to CIF format. Accepts a dictionary of PDB contents and returns a dictionary of converted CIF contents with the same keys. The node uses BioPython for parsing/writing and includes a best-effort fix to map atom records to entity IDs for protein chains.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbtocifnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when downstream tools or analyses require CIF (mmCIF) format instead of PDB. Typical workflow: load or assemble a batch of PDB structures, convert them to CIF with this node, then feed the CIF outputs into subsequent structure processing, validation, or visualization nodes that expect CIF.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures to convert. Keys are structure IDs (names), values are PDB file contents as strings.</td><td style="word-wrap: break-word;">{'example_value': {'my_structure': 'ATOM      1  N   MET A   1      11.104  13.207   2.103  1.00 20.00           N  ...', 'another_structure': 'ATOM      1  N   ALA B   1       5.234  -2.151   8.930  1.00 15.00           N  ...'}}</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.cif</td><td style="word-wrap: break-word;">CIF</td><td style="word-wrap: break-word;">Dictionary of CIF structures converted from the input PDBs. Keys mirror the input keys; values are CIF contents as strings.</td><td style="word-wrap: break-word;">{'example_value': {'my_structure': 'data_template\n#\n_loop\n_atom_site.group_PDB _atom_site.id _atom_site.type_symbol ...', 'another_structure': 'data_template\n#\n_loop\n_atom_site.group_PDB _atom_site.id _atom_site.type_symbol ...'}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: The input must be a non-empty dictionary mapping structure names to PDB content strings.
- **Batch-friendly**: Multiple PDBs can be converted in one run; empty entries are skipped.
- **Entity mapping**: The node attempts to improve atom-to-entity mappings for protein chains in the resulting CIF; this is heuristic and may not adjust all cases.
- **Dependencies**: Requires BioPython (including PDB and mmCIF modules) to be available in the environment.
- **Error handling**: If none of the inputs can be converted, the node raises an error.
- **Output structure**: The output dictionary preserves the original input keys for easy tracking.

## Troubleshooting
- **Error: 'PDB input must be a non-empty dictionary'**: Ensure you pass a dict like {"name": "<pdb_string>"} and that strings are not empty or whitespace.
- **Conversion failed for a specific structure**: Verify the PDB content is valid and parsable by BioPython; remove nonstandard lines or fix formatting issues.
- **Missing or incorrect entity IDs in CIF**: The node includes a best-effort fix for protein chains; if issues persist, consider using specialized CIF post-processing tools.
- **Environment errors (missing modules)**: Install BioPython and its PDB/mmCIF I/O components in the runtime environment.
- **Partial conversion (some entries missing)**: Empty or invalid PDB strings are skipped; check logs and ensure each dict value contains valid PDB content.
