# Load PDB

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads a Protein Data Bank (PDB) structure provided as plain text and exposes it as a structured PDB output for downstream biotech nodes. It stores the content under a user-defined identifier without modifying or validating the text, allowing existing PDB data to be injected directly into a workflow.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loadpdbnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a PDB structure as text (for example, from a file or external service) and need to introduce it into a biotech workflow. Assign a unique pdb_id; if you will pair this with a FASTA sequence later, ensure the FASTA header ID exactly matches this pdb_id. Connect the resulting PDB output to nodes that accept PDB inputs such as visualization, structure manipulation, sequence extraction, or combiner/batching nodes.

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
<tr><td style="word-wrap: break-word;">pdb_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The full PDB file content as a single text string. The content is passed through unchanged and is expected to be valid PDB-format text for downstream consumers.</td><td style="word-wrap: break-word;">HEADER    TEST STRUCTURE\nATOM      1  N   MET A   1      11.104  13.207   9.101  1.00 20.00           N\nATOM      2  CA  MET A   1      12.560  13.420   9.320  1.00 20.00           C\nEND</td></tr>
<tr><td style="word-wrap: break-word;">pdb_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier assigned to this PDB entry within the workflow. Should be unique, especially when using multiple PDB structures. For workflows that pair structures with sequences, this should match the corresponding FASTA sequence ID.</td><td style="word-wrap: break-word;">my_protein</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A PDB-typed output representing a mapping from the provided pdb_id to the raw PDB text content. This structured output is suitable for any downstream node that accepts PDB inputs and may be combined with other PDB mappings.</td><td style="word-wrap: break-word;">{"my_protein": "ATOM      1  N   MET A   1      11.104  13.207   9.101  1.00 20.00           N\n...\nEND"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Unique identifiers**: Use distinct pdb_id values for each structure to prevent collisions or overwriting when structures are merged or batched.
- **FASTA integration**: When pairing with FASTA data, the FASTA header or sequence ID must exactly match the pdb_id used in this node for correct alignment and mapping.
- **No format checking**: The node does not validate or repair PDB formatting. Invalid or nonstandard content may cause downstream parsing or analysis nodes to fail.
- **Text-only handling**: The PDB content is treated purely as text. For best compatibility, ensure it follows standard PDB conventions, including correct record types and a terminating END line.

## Troubleshooting
- **No output or empty structure**: Confirm pdb_string is not empty and that any upstream connections supplying it are correctly configured.
- **Downstream parse failures**: If later nodes report parsing errors, inspect the PDB text for correct formatting (record columns, ATOM/HETATM lines, required header/footer such as END).
- **ID mismatch with FASTA**: If pairing nodes cannot find matching entries, verify that pdb_id here exactly matches the FASTA sequence ID, including capitalization and any prefixes or suffixes.
- **Overwritten entries in combined results**: If some structures appear to be missing after combining PDBs, check for duplicated pdb_id values across multiple Load PDB nodes and rename them to ensure uniqueness.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/loadpdbnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

