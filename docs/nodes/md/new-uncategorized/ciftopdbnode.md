# CIF to PDB

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts macromolecular CIF (mmCIF) structures to PDB format. Accepts one or more CIF structures and returns PDB text for each, preserving the input names as keys. Skips empty entries and raises clear errors for invalid inputs.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/ciftopdbnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have structures in CIF/mmCIF format and need PDB output for downstream modeling, visualization, or tools that require PDB. Typical workflow: import or generate CIF structures, convert them here, then pass the resulting PDBs to analysis, design, or fixing/cleanup nodes.

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
<tr><td style="word-wrap: break-word;">cif</td><td>True</td><td style="word-wrap: break-word;">CIF</td><td style="word-wrap: break-word;">Dictionary mapping structure names to CIF/mmCIF text content. Each value must be a valid CIF string containing atomic coordinates (atom_site).</td><td style="word-wrap: break-word;">{'1abc_model.cif': 'data_1abc ... loop_ _atom_site.group_PDB _atom_site.id ...', 'complexA.cif': 'data_complexA ... loop_ _atom_site.group_PDB _atom_site.id ...'}</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary mapping the same structure names to converted PDB text content.</td><td style="word-wrap: break-word;">{'1abc_model.cif': 'HEADER ...\\nATOM      1  N   MET A   1 ...\\nTER\\nEND\\n', 'complexA.cif': 'HEADER ...\\nATOM      1  N   ALA B   1 ...\\nTER\\nEND\\n'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: The input must be a non-empty dictionary {name: cif_text}. An error is raised otherwise.
- **Empty values**: Entries with empty or whitespace-only CIF content are skipped.
- **Failure behavior**: If no entries can be converted, the node raises an error.
- **Metadata differences**: PDB format may not retain all mmCIF annotations (e.g., rich metadata or some restraint tables).
- **Model handling**: If the CIF contains multiple models, the PDB output may contain multiple MODEL records; ensure downstream tools support this.
- **Chemistry validation**: The conversion does not validate chemistry or fix structural issues; it only changes file format.

## Troubleshooting
- **Error: 'CIF input must be a non-empty dictionary'**: Provide a dictionary with at least one valid CIF string.
- **Error converting a specific entry**: The CIF content may be malformed or missing atom_site coordinates. Verify the file integrity and that it is mmCIF with atomic data.
- **Output is empty or missing entries**: Empty inputs are skipped; ensure each value contains non-empty CIF text.
- **Downstream tool rejects PDB**: Some tools require single-model or specific record formatting. Consider splitting multi-model PDBs or cleaning the structure with a fixer node.
- **Lost annotations**: If you rely on mmCIF-specific metadata, keep the original CIF alongside the PDB for reference.
