# PDB To CIF

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts protein structure data from PDB format to CIF (mmCIF) format. Accepts one or more PDB structures as a dictionary and returns corresponding CIF strings. The node validates inputs, skips empty entries, and attempts to improve chain/entity mapping for protein chains during conversion.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/pdbtocifnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need CIF/mmCIF outputs for downstream tools or services that require CIF instead of PDB. Typical workflow: load or batch multiple PDB structures, convert them with PDB To CIF, then pass the CIF output to analysis, visualization, or conversion-back nodes as needed.

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
<tr><td style="word-wrap: break-word;">pdb</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures to convert. Keys are structure names/IDs, values are PDB content strings.</td><td style="word-wrap: break-word;">{'example_structure': 'ATOM ... (PDB content) ...'}</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.cif</td><td style="word-wrap: break-word;">CIF</td><td style="word-wrap: break-word;">Dictionary with the same keys as input, where each value is the converted CIF (mmCIF) content string.</td><td style="word-wrap: break-word;">{'example_structure': 'data_example\n#\nloop_\n_atom_site... (CIF content) ...'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- This node requires a non-empty dictionary input; passing a single PDB string directly will fail. Use a loader node that outputs a PDB dictionary.
- Empty PDB entries are skipped; if all entries are empty or invalid, the node raises an error.
- The node uses a PDB-to-CIF conversion backend and may raise an error for malformed or non-standard PDB files.
- For multi-chain protein structures, the node attempts to adjust atom/entity mappings in the resulting CIF to improve consistency. Non-protein chains are still converted without this adjustment.
- Outputs preserve input keys (structure names/IDs) for consistent downstream mapping.

## Troubleshooting
- Error: 'PDB input must be a non-empty dictionary' — Ensure you provide a dict like {"id": "PDB string"}. Use nodes that output type PDB.
- Error: 'Conversion failed for PDB <name>: ...' — The PDB may be malformed or contain unsupported records. Validate or clean the input PDB file.
- No output or empty CIF for a structure — Check that the input PDB value is not an empty string and contains valid ATOM/HETATM records.
- Only some entries converted — Review logs for skipped entries and fix or remove problematic PDBs.
- Downstream tool rejects CIF — While conversion generally follows standard mmCIF, tool-specific requirements may vary. Validate CIF with external validators if necessary.
