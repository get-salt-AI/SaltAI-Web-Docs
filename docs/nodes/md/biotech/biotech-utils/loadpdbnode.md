# Load PDB

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads a Protein Data Bank (PDB) structure provided as a raw text string and packages it into a structured output for downstream biotech nodes. You assign an identifier to the PDB, and the node emits a mapping from that ID to the provided PDB content. The node does not modify or validate the PDB format.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loadpdbnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node at the start of workflows when you already have PDB content (e.g., pasted text or preloaded data) and need to pass it to structure-processing or visualization nodes. It is commonly paired with nodes that consume PDB structures or with sequence nodes where the PDB ID should match a corresponding FASTA record.

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
<tr><td style="word-wrap: break-word;">pdb_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The full PDB file content as text. This should be a valid PDB-formatted string.</td><td style="word-wrap: break-word;">HEADER    EXAMPLE PDB ATOM      1  N   MET A   1      20.154  34.198  27.447  1.00 20.00           N ATOM      2  CA  MET A   1      21.560  34.560  27.800  1.00 20.00           C TER END </td></tr>
<tr><td style="word-wrap: break-word;">pdb_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">An identifier to associate with this PDB entry. Must be unique within the workflow. If you also provide a FASTA record for this structure elsewhere in the workflow, its sequence ID should match this value.</td><td style="word-wrap: break-word;">pdb</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A dictionary mapping the provided PDB ID to the PDB string content.</td><td style="word-wrap: break-word;">{'my_structure': 'HEADER    EXAMPLE PDB\nATOM      1  N   MET A   1      20.154  34.198  27.447  1.00 20.00           N\nEND\n'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **ID uniqueness**: Use unique pdb_id values to avoid collisions when working with multiple structures.
- **FASTA alignment**: If you include a corresponding FASTA record, ensure its sequence ID exactly matches the pdb_id.
- **No validation**: The node does not validate PDB formatting; malformed content may cause downstream nodes to fail.
- **Pass-through behavior**: The node does not alter the PDB content; it wraps your input into a keyed structure for workflow compatibility.

## Troubleshooting
- **Downstream errors when parsing PDB**: Verify pdb_string is valid PDB format; correct formatting or regenerate the structure.
- **Unexpected overwriting or missing entries**: Ensure each pdb_id is unique across the workflow to prevent key collisions.
- **Empty or truncated output**: Check that the entire PDB text was provided (including END/TER records where applicable) and that there are no hidden characters removed during copy/paste.
- **Mismatch with FASTA-based steps**: Align the pdb_id with the sequence ID used in related FASTA inputs.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/loadpdbnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

