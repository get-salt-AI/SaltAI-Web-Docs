# Load PDB

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Loads a protein structure from a raw PDB-formatted string and tags it with a user-provided ID. It produces a PDB-typed output as a dictionary mapping the given ID to the provided PDB string. The node performs no parsing or validation of PDB content; it simply packages the data for downstream nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/LoadPDBNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you already have a PDB text block (e.g., from a file or external source) and need to introduce it into a workflow that operates on PDB structures. Commonly placed at the start of structure-processing pipelines, it allows you to assign a unique ID so later nodes can reference the structure consistently and correlate it with associated sequence or alignment data.

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
<tr><td style="word-wrap: break-word;">pdb_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The full PDB-formatted text content of a protein structure. This should be the raw text of the PDB file.</td><td style="word-wrap: break-word;">ATOM      1  N   MET A   1      38.428  13.337   2.441  1.00 54.69           N  ...</td></tr>
<tr><td style="word-wrap: break-word;">pdb_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">A unique identifier to associate with this PDB structure. If a corresponding sequence (FASTA) is used elsewhere, ensure its sequence ID matches this value.</td><td style="word-wrap: break-word;">pdb1</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.pdb</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">A PDB-typed dictionary with the provided pdb_id as the key and the PDB string as the value.</td><td style="word-wrap: break-word;">{'pdb1': 'ATOM      1  N   MET A   1      38.428  13.337   2.441  1.00 54.69           N  ...'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Uniqueness of IDs**: Use unique pdb_id values if multiple PDBs are present in the same workflow to avoid collisions.
- **Sequence alignment**: If you provide a matching FASTA sequence elsewhere in the workflow, its sequence ID should match the pdb_id.
- **No validation**: The node does not validate or parse the PDB text; ensure the content is in valid PDB format if downstream nodes require it.
- **Pass-through behavior**: The output is a simple mapping {pdb_id: pdb_string}, intended for downstream nodes expecting a PDB-typed input.

## Troubleshooting
- **Downstream parse errors**: If later nodes fail to read the structure, verify the pdb_string is complete, properly formatted PDB text.
- **ID mismatch with FASTA**: If a sequence-to-structure association fails, ensure the FASTA sequence ID matches the pdb_id used here.
- **Overwritten or ambiguous structures**: If multiple structures seem to conflict downstream, confirm all pdb_id values are unique within the workflow.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/LoadPDBNode/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

