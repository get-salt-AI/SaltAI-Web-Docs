# Load PDB

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads a Protein Data Bank (PDB) structure provided as plain text and exposes it as a structured PDB output for downstream biotech nodes. It stores the content under a user-defined identifier without modifying or validating the text. Use this node to introduce existing PDB data directly into a workflow.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loadpdbnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use when you have a PDB structure in text form (e.g., copied from a file or fetched elsewhere) and want to feed it into a biotech workflow. Provide a unique ID for the structure; if you plan to pair it with a FASTA sequence later, ensure the FASTA sequence header ID matches this PDB ID. Connect the output to nodes that consume PDB inputs (e.g., visualization, sequence extraction, chain operations, or batch/combiner nodes).

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
<tr><td style="word-wrap: break-word;">pdb_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The full PDB file content as a text string. The content is passed through unchanged to downstream nodes.</td><td style="word-wrap: break-word;">HEADER    TEST STRUCTURE\nATOM      1  N   MET A   1      11.104  13.207   9.101  1.00 20.00           N\nATOM      2  CA  MET A   1      12.560  13.420   9.320  1.00 20.00           C\nEND</td></tr>
<tr><td style="word-wrap: break-word;">pdb_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier assigned to this PDB entry. Must be unique within the workflow if multiple PDBs are used. If pairing with a FASTA record, the FASTA sequence ID should match this value.</td><td style="word-wrap: break-word;">my_protein</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A dictionary mapping the provided PDB ID to the PDB text content, suitable for use by nodes that accept PDB inputs.</td><td style="word-wrap: break-word;">{'my_protein': 'ATOM      1  N   MET A   1      11.104  13.207   9.101  1.00 20.00           N\\n...\\nEND'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- IDs should be unique across the workflow to avoid collisions when combining or batching structures.
- If you intend to pair this PDB with a FASTA sequence, ensure the FASTA header ID matches the provided pdb_id.
- The node does not validate or correct PDB formatting; downstream nodes may fail if the content is malformed.
- Content is treated as plain text; ensure it follows standard PDB conventions (e.g., ATOM/HETATM records, END line) for best compatibility.

## Troubleshooting
- Empty or missing output: Ensure pdb_string is not empty and contains valid PDB text.
- Downstream node errors about parsing: Verify the PDB content is properly formatted (correct columns, ATOM/HETATM lines, and an END record).
- ID mismatch when pairing with FASTA: Update either pdb_id here or the FASTA header so they match exactly.
- Conflicts in combined workflows: If merging multiple PDB inputs later, make sure each pdb_id is unique to avoid overwriting entries.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/loadpdbnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

