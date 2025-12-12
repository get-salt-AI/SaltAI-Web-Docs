# CIF To PDB

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts one or more protein structures from CIF (mmCIF) format to PDB format. Accepts a dictionary of CIF name-to-content pairs, converts each valid entry, and returns a dictionary with the same keys mapped to PDB strings. Skips empty entries and raises clear errors on invalid input or failed conversions.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/ciftopdbnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have structure data in CIF (mmCIF) format and need PDB for downstream steps such as visualization, sequence extraction, or chain manipulation. Typical workflow: Load CIF data → CIF To PDB → nodes that require PDB input (e.g., PDB Visualization, PDB To FASTA, Chain Extractor, or Save nodes).

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
<tr><td style="word-wrap: break-word;">cif</td><td>True</td><td style="word-wrap: break-word;">CIF</td><td style="word-wrap: break-word;">Dictionary of CIF structures to convert. Keys are structure identifiers; values are CIF file contents as strings.</td><td style="word-wrap: break-word;">{'my_structure': 'data_...\\nloop_\\n_atom_site...\\n'}</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary with the same keys as the input, where each value is the converted PDB string content.</td><td style="word-wrap: break-word;">{'my_structure': 'HEADER ...\\nATOM ...\\nEND\\n'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: The input must be a non-empty dictionary mapping names to CIF text. Supplying a plain string or an empty dict will fail.
- **Empty entries**: Any entries with empty or whitespace-only CIF content are skipped; if all entries are skipped, the node raises an error.
- **Conversion fidelity**: PDB format may not preserve all mmCIF-specific annotations. Expect potential loss or reformatting of some metadata.
- **Key preservation**: Output dictionary keys mirror the input names, enabling consistent tracking across workflow steps.
- **Errors**: Malformed or unsupported CIF content will raise a conversion error for that entry.
- **Dependencies**: Requires a working structural biology backend for parsing and writing (e.g., a compatible BioPython environment).

## Troubleshooting
- **'CIF input must be a non-empty dictionary'**: Ensure you pass a dict like {"id": "cif_text"} and not a single string. Verify the dict is not empty.
- **'No CIF structures could be converted to PDB'**: All provided CIF entries were empty or failed conversion. Check that each value is non-empty CIF text.
- **Conversion failed for a specific entry**: The CIF may be malformed. Validate the file contents or try another source/version of the structure.
- **Unexpected or missing metadata in PDB**: PDB cannot represent all mmCIF fields. If you need specific annotations, keep the original CIF alongside the PDB.
- **Large structures or performance issues**: Very large CIFs can be slow to parse/convert. Try converting fewer structures at a time.
