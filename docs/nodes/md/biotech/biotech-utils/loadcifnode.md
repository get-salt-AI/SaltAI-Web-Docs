# Load CIF

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads a CIF/mmCIF structure from a text string and assigns it a user-defined ID. Produces a dictionary mapping that ID to the CIF content so it can be consumed by downstream biotech nodes that operate on structure data.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loadcifnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the beginning of workflows where you need to inject a protein structure in CIF/mmCIF format. Provide the full CIF text in the cif_string input and set a unique cif_id. Connect the output to nodes that accept CIF structures or to steps that store, transform, or analyze structure data.

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
<tr><td style="word-wrap: break-word;">cif_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The complete CIF or mmCIF contents as plain text. This should be the raw file content, not a filename or URL.</td><td style="word-wrap: break-word;">data_1ABC # _entry.id 1ABC _atom_site.group_PDB ATOM ...</td></tr>
<tr><td style="word-wrap: break-word;">cif_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A unique identifier for this structure entry. Often matches an associated FASTA record ID so sequence and structure can be aligned downstream.</td><td style="word-wrap: break-word;">structure</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.cif</td><td style="word-wrap: break-word;">CIF</td><td style="word-wrap: break-word;">A dictionary mapping the provided cif_id to the CIF/mmCIF text string. This format is designed for nodes that accept a structure dictionary keyed by ID.</td><td style="word-wrap: break-word;">{ "structure": "<cif-string>" }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Unique IDs**: Use a distinct cif_id for each structure to prevent collisions when handling multiple structures in the same workflow.
- **Content format**: The node expects raw CIF/mmCIF text, not a path, URL, or binary file handle.
- **FASTA alignment**: If you also provide a corresponding FASTA sequence elsewhere, ensure the FASTA record ID matches cif_id so downstream nodes can associate sequence and structure.
- **Batch handling**: The output is a simple {id: content} dictionary, which integrates naturally with other biotech nodes that operate on collections of structures.

## Troubleshooting
- **Empty output or runtime errors**: Confirm that cif_string is non-empty and contains valid CIF/mmCIF data (not just comments or headers).
- **ID conflicts in downstream nodes**: If another node reports duplicate or conflicting IDs, change cif_id to a unique value and re-run.
- **Format or parsing warnings downstream**: Ensure that the CIF text is complete and correctly formatted, with no truncated sections or missing data blocks.
- **Unexpected behavior in receiving node**: Verify the downstream node accepts the CIF type and that you connected the structure.cif output to the correct input field.
