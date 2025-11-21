# PDB To CIF

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts one or more PDB structures to CIF format using BioPython. The node parses each PDB, writes an mmCIF representation, and applies a light post-processing step to improve atom-to-entity mappings based on detected protein chains.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbtocifnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need CIF (mmCIF) files from PDB inputs for downstream tools that prefer or require CIF format. Provide a dictionary mapping names to PDB content; the node returns a dictionary with the same keys mapped to CIF strings. Commonly placed after nodes that load or generate PDB and before nodes that require CIF.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structure content to convert. Keys are structure names (identifiers), values are PDB file contents as strings.</td><td style="word-wrap: break-word;">{'1ABC': 'HEADER ...\\nATOM      1  N   MET A   1 ...\\nTER\\nEND\\n', 'design_model': 'ATOM      1  N   GLY B   1 ...\\nEND\\n'}</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.cif</td><td style="word-wrap: break-word;">CIF</td><td style="word-wrap: break-word;">Dictionary of converted CIF structures. Keys mirror the input names; values are CIF (mmCIF) contents as strings.</td><td style="word-wrap: break-word;">{'1ABC': 'data_template\\nloop_\\n_atom_site.group_PDB ...', 'design_model': 'data_template\\nloop_\\n_atom_site.group_PDB ...'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: The input must be a non-empty dictionary {name: pdb_string}. Empty or whitespace-only entries are skipped.
- **Dependencies**: Requires BioPython's PDB modules (e.g., PDBParser, MMCIFIO) to be available.
- **Entity mapping fix**: After conversion, the node attempts to fix atom-to-entity label mappings for protein chains. This adjustment is heuristic and may not cover all edge cases.
- **Protein chains only**: Chain detection for mapping focuses on protein residues (standard amino acids). Non-protein chains may not receive adjusted entity IDs.
- **Temporary files**: The node writes temporary .pdb and .cif files during conversion and cleans them up afterward.
- **Error handling**: If any conversion fails, the node raises a ValueError with a message indicating which structure failed.
- **Multi-structure support**: Processes multiple structures in a single run and returns a dictionary of outputs with the same keys.

## Troubleshooting
- **Conversion failed for a structure**: Ensure the PDB content is valid and complete. Malformed headers or atom lines can cause BioPython parsing errors.
- **Module not found (Bio.PDB)**: Install BioPython in the environment and verify the relevant PDB modules are available.
- **Empty output or missing keys**: Check that input values are non-empty strings. Empty entries are skipped and may result in missing outputs.
- **Incorrect entity IDs in CIF**: The heuristic mapping might not fit specialized molecules or modified residues. Consider post-processing the CIF or adjusting inputs.
- **Chain not detected**: If a protein chain contains only non-standard residues, it may be missed by the protein-only filter, reducing mapping fixes.
