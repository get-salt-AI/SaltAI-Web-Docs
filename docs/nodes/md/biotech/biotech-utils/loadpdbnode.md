# Load PDB

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads a Protein Data Bank (PDB) structure from a text string and assigns it a user-defined identifier. Produces a dictionary mapping the provided ID to the PDB content, making it easy to reference and combine multiple structures in a workflow.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loadpdbnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a PDB file content (as text) that you want to introduce into a workflow. Provide the full PDB string and an ID to identify it. The output connects to nodes that visualize, convert, extract chains, convert to FASTA, or combine multiple PDBs.

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
<tr><td style="word-wrap: break-word;">pdb_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Full PDB content as a text string. Should be valid PDB format.</td><td style="word-wrap: break-word;">HEADER EXAMPLE PDB\nATOM      1  N   MET A   1      11.104  13.207   2.100  1.00 20.00           N\nEND</td></tr>
<tr><td style="word-wrap: break-word;">pdb_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier assigned to this PDB entry. Must be unique within a batch of PDBs. If you provide a corresponding FASTA record, its sequence ID should match this value.</td><td style="word-wrap: break-word;">my_structure</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary mapping the provided ID to the PDB string content.</td><td style="word-wrap: break-word;">{'my_structure': 'HEADER EXAMPLE PDB\\nATOM ...\\nEND'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- IDs should be unique if multiple PDBs are used in the same workflow.
- If a corresponding FASTA record is used later, ensure its sequence ID matches the pdb_id.
- The output is a dictionary {pdb_id: pdb_string}; downstream nodes expect this structure.
- Provide valid PDB-formatted text; malformed content may cause downstream failures.

## Troubleshooting
- PDB not recognized downstream: Verify the input is valid PDB format (header/ATOM/HETATM records present).
- ID conflicts when combining PDBs: Ensure each pdb_id is unique across all inputs before passing to combiner/batch nodes.
- FASTA mismatch warnings later: Align the FASTA sequence ID to match the pdb_id used here.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/loadpdbnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

