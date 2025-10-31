# PDB To CIF

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts one or more PDB structures into CIF format using BioPython. Accepts a dictionary of PDB strings and returns a dictionary of corresponding CIF strings. The node also attempts to improve chain-to-entity mappings in the resulting CIF to better reflect protein chain entities.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/PDBToCIFNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when downstream tools require CIF instead of PDB. Typical workflow: load or assemble your PDB data (e.g., with Load PDB, PDB Chain Extractor, or PDB Combiner), then pass the result to PDB To CIF. The output CIF dictionary can be saved or fed into analysis or modeling nodes that expect CIF input.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structure contents to convert, formatted as {pdb_name: pdb_content}. Each value should be a valid PDB text string.</td><td style="word-wrap: break-word;">{'example_dict': {'my_structure': 'ATOM      1  N   MET A   1      ...', 'complex_A': 'ATOM      1  N   ALA B   1      ...'}}</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.cif</td><td style="word-wrap: break-word;">CIF</td><td style="word-wrap: break-word;">Dictionary of CIF structures converted from the provided PDB input, keyed by the original names.</td><td style="word-wrap: break-word;">{'example_dict': {'my_structure': 'data_template\n##_atom_site ...', 'complex_A': 'data_template\n##_atom_site ...'}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: The input must be a non-empty dictionary mapping IDs to PDB text. Empty or whitespace-only entries are skipped and may cause errors if no valid entries remain.
- **Entity mapping adjustment**: After conversion, the node attempts to correct chain-to-entity mappings in the CIF, which can improve compatibility with tools that rely on label_entity_id.
- **Protein chain focus**: The entity mapping adjustment focuses on protein chains detected by standard amino-acid residue names; non-protein chains are not guaranteed to be mapped.
- **Batch-friendly**: Multiple PDBs can be converted in one call; outputs preserve the original keys.
- **Dependencies**: Requires BioPython with PDB and mmCIF I/O support.
- **Temporary files**: The node uses temporary files for conversion and cleans them up after processing.

## Troubleshooting
- **Conversion failed: 'Failed to convert PDB to CIF'**: Ensure each PDB string is valid and complete. Corrupted or non-standard formatting can cause parsing failures.
- **Empty output or error about no structures converted**: Verify the input dictionary is not empty and that each value contains non-empty PDB content.
- **Unexpected chain/entity mapping in CIF**: Some complex or non-protein chains may not map as expected. Consider simplifying the input or reviewing chain IDs to confirm they represent protein chains.
- **Performance issues with very large structures**: Large PDBs may take longer to parse and convert. Reduce input size or convert structures individually when needed.
- **Mismatched keys**: Output keys mirror input keys. If you expect a specific name, ensure your input dictionary uses that exact key.
