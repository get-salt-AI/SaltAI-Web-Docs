# PDB To CIF

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts one or more PDB structures to mmCIF format using BioPython. Accepts a dictionary of PDB IDs to PDB content strings and returns a dictionary of the same IDs mapped to converted mmCIF strings. Includes basic handling to improve chain/entity mappings in the generated mmCIF where possible.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbtocifnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need mmCIF structures for downstream tools that require mmCIF instead of PDB. Typical workflow: load or prepare PDB content, optionally clean/fix it, then convert to mmCIF for analysis, visualization, or as input to other biotech nodes.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures to convert. Keys are structure IDs (names) and values are PDB-formatted strings.</td><td style="word-wrap: break-word;">{'example_pdb': 'ATOM      1  N   MET A   1      11.104  13.207   8.678  1.00 20.00           N  ...'}</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.cif</td><td style="word-wrap: break-word;">CIF</td><td style="word-wrap: break-word;">Dictionary of converted mmCIF structures. Keys mirror the input IDs; values are mmCIF-formatted strings.</td><td style="word-wrap: break-word;">{'example_pdb': 'data_template\n#\nloop_\n_atom_site.group_PDB ...\n'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: The input must be a non-empty dictionary mapping IDs to valid PDB text. Invalid or empty strings are skipped and may cause the node to error if no valid entries remain.
- **Output mapping**: Output keys match input keys, enabling consistent tracking of structures through the workflow.
- **Entity/chain mapping**: The node attempts to improve entity/chain mappings in the mmCIF output, but results may vary depending on the input structure; verify the output if downstream tools rely on specific entity IDs.
- **Error behavior**: If no structures can be converted, the node raises an error rather than returning an empty result.
- **Content expectation**: Provide complete PDB content strings (not file paths). Large structures are supported, but malformed PDB input may fail conversion.

## Troubleshooting
- **Error: 'PDB input must be a non-empty dictionary'**: Ensure you pass a dictionary with at least one key-value pair where the value is a valid PDB string.
- **Conversion failed for specific entry**: Validate the PDB text for that entry (formatting, missing headers/records). Try fixing the structure with a PDB repair step before conversion.
- **Empty output or missing entries**: Inputs with empty or whitespace-only content are skipped. Ensure each value contains valid PDB content.
- **Unexpected or missing entity IDs in mmCIF**: Check the mmCIF for chain/entity labeling. If downstream tools require precise entity IDs, consider pre-processing/fixing the PDB or manually reviewing the generated mmCIF.
- **Downstream tool rejects mmCIF**: Confirm that the receiving tool supports the generated mmCIF flavor and that required categories/fields are present. If necessary, re-validate or re-export from another tool after conversion.
