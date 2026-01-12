# Load CIF

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads a CIF/mmCIF structure from a text string and assigns it a user-defined ID. Produces a dictionary mapping the provided ID to the CIF content for use by downstream biotech nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loadcifnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of workflows where you need to provide a protein structure in CIF/mmCIF format. Paste the CIF text into the input and set a unique ID. The output can be routed into nodes that accept CIF structures or saved/exported.

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
<tr><td style="word-wrap: break-word;">cif_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The full CIF/mmCIF content as a text string.</td><td style="word-wrap: break-word;">data_1ABC # _entry.id 1ABC _atom_site.group_PDB ATOM ...</td></tr>
<tr><td style="word-wrap: break-word;">cif_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A unique identifier to associate with this structure. If you also provide a related FASTA elsewhere, use a matching sequence ID.</td><td style="word-wrap: break-word;">structure</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.cif</td><td style="word-wrap: break-word;">CIF</td><td style="word-wrap: break-word;">A dictionary mapping the provided ID to the CIF string.</td><td style="word-wrap: break-word;">{'structure': '<cif-string>'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Unique IDs**: Use a unique cif_id for each structure to avoid collisions when handling multiple structures.
- **Content format**: The node expects raw CIF/mmCIF text, not a file path or URL.
- **FASTA alignment**: If you plan to use a FASTA sequence corresponding to this structure, ensure the FASTA record ID matches the cif_id.
- **Batch handling**: The output is a dictionary {id: content}, which fits naturally into nodes that operate on structure dictionaries.

## Troubleshooting
- **Empty output or errors**: Ensure cif_string is not empty and contains valid CIF/mmCIF content.
- **ID conflicts**: If downstream nodes report duplicate IDs, change cif_id to a unique value.
- **Invalid format warnings**: If other nodes complain about parsing, confirm the CIF text is complete and correctly formatted (no truncated lines or missing data blocks).
- **Unexpected downstream behavior**: Verify that the receiving node accepts a CIF type and that the output is correctly connected.
