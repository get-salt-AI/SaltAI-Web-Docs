# Load PDB

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Ingests a Protein Data Bank (PDB) structure as a raw text string and packages it into a structured PDB output for downstream nodes. The node labels the structure with a user-provided ID and outputs a dictionary mapping that ID to the PDB content. Designed to initialize workflows from directly pasted or programmatically generated PDB text.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loadpdbnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have a PDB file's content as text (e.g., pasted from a file or fetched via an API) and want to introduce it into a workflow. Set a unique pdb_id to distinguish it from other structures, and match this ID to related sequence/MSA nodes when required by downstream components.

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
<tr><td style="word-wrap: break-word;">pdb_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The full PDB file content as a text string. Supports multiline input. Provide the entire PDB text (ATOM/HETATM records, headers as applicable).</td><td style="word-wrap: break-word;">HEADER    PROTEIN STRUCTURE\nATOM      1  N   MET A   1      11.104  13.207  10.234  1.00 20.00           N\n...</td></tr>
<tr><td style="word-wrap: break-word;">pdb_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier to assign to this PDB entry. Must be unique if multiple PDBs are used in one workflow. If a matching FASTA is used elsewhere in the workflow, its sequence ID should match this value.</td><td style="word-wrap: break-word;">my_protein</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A structured PDB output represented as a dictionary mapping pdb_id to pdb_string. This format is expected by other biotech nodes and batch combiners.</td><td style="word-wrap: break-word;">{'my_protein': 'HEADER    PROTEIN STRUCTURE\\nATOM ...'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **IDs must be unique**: When using multiple PDBs in one workflow or batching, ensure each pdb_id is unique to avoid conflicts.
- **ID matching with sequences**: If you provide a related FASTA or other sequence input, its sequence ID should match the pdb_id for proper alignment in downstream steps.
- **Input format**: This node accepts raw PDB text, not a file path or URL.
- **Output structure**: The output is a dictionary of {pdb_id: pdb_string}, which downstream nodes rely on.
- **Multiline input**: Provide the full, multiline PDB content; partial content may cause downstream parsing failures.

## Troubleshooting
- **Duplicate ID error in batching**: If later nodes report non-unique IDs, change pdb_id here to a unique value.
- **Downstream parsing failures**: If visualization or processing fails, verify that pdb_string is valid PDB format and not truncated.
- **Mismatched IDs with FASTA/A3M**: If alignment or pairing steps fail, ensure the pdb_id matches the sequence ID used in related nodes.
- **Empty or minimal PDB content**: Ensure pdb_string contains complete ATOM/HETATM records; empty or header-only inputs can break downstream tools.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/loadpdbnode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

