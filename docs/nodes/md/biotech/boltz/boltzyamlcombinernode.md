# Boltz YAML Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines molecular building blocks into a valid Boltz YAML configuration. It merges sequences (required) with optional constraints, templates, and properties, validates structure and chain ID uniqueness, and prepares auxiliary files (e.g., MSA and template PDB/CIF) referenced by filename in the YAML.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzyamlcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this after assembling lists with Boltz List Combiner. Connect the combined sequences (and optionally constraints, templates, and properties). The node outputs a Boltz YAML dictionary and a file-bundle dictionary that upstream prediction nodes consume.

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
<tr><td style="word-wrap: break-word;">sequences</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">List of sequence objects (proteins, nucleic acids, or ligands). Typically created by Boltz Sequence Builder/Protein/Ligand nodes and merged by Boltz List Combiner.</td><td style="word-wrap: break-word;">[{"protein": {"id": "A", "sequence": "MSEQUENCE", "msa": "empty", "_sequence_name": "protA"}}, {"ligand": {"id": "L", "smiles": "C1=CC=CC=C1"}}]</td></tr>
<tr><td style="word-wrap: break-word;">constraints</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of constraint objects (e.g., pocket, bond, contact) from Boltz Constraint Builder combined via Boltz List Combiner.</td><td style="word-wrap: break-word;">[{"pocket": {"binder": "L", "contacts": [["A", 10], ["A", 25]], "max_distance": 6.0}}]</td></tr>
<tr><td style="word-wrap: break-word;">templates</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of template objects that include structure content (PDB/CIF). The node converts embedded content to filenames and adds them to the file bundle.</td><td style="word-wrap: break-word;">[{"pdb": "<pdb-file-content>", "chain_id": "A"}]</td></tr>
<tr><td style="word-wrap: break-word;">properties</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of property objects (e.g., affinity specifications) from Boltz Property Builder combined via Boltz List Combiner.</td><td style="word-wrap: break-word;">[{"affinity": {"binder": "L"}}]</td></tr>
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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">Validated Boltz YAML dictionary with sequences and any provided constraints, templates, and properties. Includes version and filename references to auxiliary files.</td><td style="word-wrap: break-word;">{"version": 1, "sequences": [{"protein": {"id": "A", "sequence": "MSEQUENCE", "msa": "msa_1.a3m"}}, {"ligand": {"id": "L", "smiles": "C1=CC=CC=C1"}}], "templates": [{"pdb": "template_2.pdb", "chain_id": "A"}], "properties": [{"affinity": {"binder": "L"}}]}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Dictionary of auxiliary files referenced by the YAML (e.g., MSA files and template structures) keyed by generated filenames.</td><td style="word-wrap: break-word;">{"msa_1.a3m": "<a3m-content>", "template_2.pdb": "<pdb-file-content>"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Unique chain IDs**: All chain IDs across all sequences must be unique; duplicates cause an error.
- **Required sequence fields**: Protein/DNA/RNA sequences must include "sequence"; ligands must include exactly one of "smiles" or "ccd".
- **MSA handling**: If a protein has an MSA that is not "empty", the content is written to a generated filename (msa_N.a3m) and the YAML references that filename.
- **Shared MSA optimization**: Proteins with identical sequence strings share the same MSA file; the YAML for those entries points to the same filename.
- **Template handling**: Template PDB/CIF content is moved to generated filenames (template_N.pdb or template_N.cif) and referenced from the YAML.
- **Validation**: The node enforces Boltz YAML structure, auto-sets version=1 if missing, and will error on invalid entity types or missing required fields.
- **Input form**: Each input can be a single object or a list; the node normalizes internally. Typically, use Boltz List Combiner to construct lists.

## Troubleshooting
- **Duplicate chain ID found**: Ensure each chain ID appears only once across all sequences (including duplicated chains from multi-chain entries).
- **Missing sequence field**: For protein/dna/rna entries, include the "sequence" field. For ligand entries, include exactly one of "smiles" or "ccd".
- **Both SMILES and CCD provided for ligand**: Provide only one field for ligands; remove the extra field.
- **No sequences provided**: The sequences input is required and must contain at least one sequence object.
- **Invalid template content**: Template objects must include valid PDB/CIF content; the node generates filenames from non-empty contents only.
- **Unexpected input type**: If inputs are raw objects instead of lists, ensure they are combined with Boltz List Combiner or pass as lists; the node accepts both.
