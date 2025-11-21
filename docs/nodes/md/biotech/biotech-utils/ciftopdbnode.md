# CIF To PDB

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Converts molecular structures from CIF (mmCIF) format to PDB format using BioPython. Accepts a dictionary of one or more CIF entries and returns a dictionary of PDB strings with the same keys. Skips empty inputs with warnings and raises clear errors for invalid data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/ciftopdbnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have structures in CIF/mmCIF format and need PDB output for downstream tools that require PDB input (e.g., visualization, chain extraction, sequence fixing). Typically, connect a CIF-producing node or a preloaded CIF dictionary to this node, then pass the resulting PDB to subsequent processing nodes.

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
<tr><td style="word-wrap: break-word;">cif</td><td>True</td><td style="word-wrap: break-word;">CIF</td><td style="word-wrap: break-word;">Dictionary of CIF structures to convert. Keys are structure IDs; values are CIF text content.</td><td style="word-wrap: break-word;">{'example_dict': {'my_structure': '<cif text content>'}}</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures converted from the input CIF entries. Keys mirror the input names.</td><td style="word-wrap: break-word;">{'example_dict': {'my_structure': '<pdb text content>'}}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input format**: The input must be a non-empty dictionary mapping names to CIF strings; otherwise the node raises an error.
- **Batch handling**: Processes multiple CIF entries in one call; empty strings are skipped with a warning.
- **Conversion fidelity**: Conversion is performed via BioPython; some mmCIF-specific annotations may not appear in the PDB output.
- **Output keys**: Output dictionary keys match the input keys, enabling easy mapping in workflows.
- **Error behavior**: If none of the CIF entries can be converted, the node raises an error.
- **Performance**: Very large structures may take longer to parse and convert.

## Troubleshooting
- **Error: 'CIF input must be a non-empty dictionary'**: Ensure you pass a dict like {"id": "<cif content>"} rather than a raw string or empty object.
- **Warning: 'Empty CIF content for <name>, skipping.'**: Provide non-empty CIF text for that entry.
- **Error: 'Failed to convert CIF <name> to PDB: ...'**: The CIF content may be malformed or unsupported. Validate the mmCIF file or re-export it from the source.
- **Error: 'No CIF structures could be converted to PDB'**: All entries were empty or invalid. Verify each input string contains valid CIF content.
- **Downstream issues with PDB consumers**: If a downstream tool complains about formatting, check that the original CIF parses correctly and consider simplifying or re-generating the structure.
