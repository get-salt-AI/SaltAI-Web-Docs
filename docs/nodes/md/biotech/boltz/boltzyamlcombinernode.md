# Boltz YAML Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a valid Boltz YAML configuration and its auxiliary files by combining lists of sequences with optional constraints, templates, and properties. It validates structure, enforces unique chain/entity IDs, normalizes fields, and converts embedded content (e.g., MSA strings, template PDB/CIF data) into file references required by downstream Boltz nodes.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzyamlcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after assembling lists with Boltz List Combiner and per-entity builders (Protein/Ligand Sequence, Constraint, Template, Property). Connect the sequences list (required) and optionally constraints, templates, and properties. The output boltz_yaml and boltz_files can then be fed into Boltz Predict or Boltz Partial Diffusion nodes.

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
<tr><td style="word-wrap: break-word;">sequences</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">List of sequence objects (proteins, DNA, RNA, ligands) produced by Boltz builders and combined by Boltz List Combiner. Can be a single item or a list.</td><td style="word-wrap: break-word;">[{'protein': {'id': 'A', 'sequence': 'MSEQNNTEMTF...', 'msa': '>'}}]</td></tr>
<tr><td style="word-wrap: break-word;">constraints</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of constraint objects combined by Boltz List Combiner. Can be a single item or a list.</td><td style="word-wrap: break-word;">[{'distance': {'id1': 'A:45', 'id2': 'B:10', 'min': 4.0, 'max': 8.0}}]</td></tr>
<tr><td style="word-wrap: break-word;">templates</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of template objects combined by Boltz List Combiner. Accepts embedded PDB or CIF content that will be converted to file references. Can be a single item or a list.</td><td style="word-wrap: break-word;">[{'pdb': 'HEADER\\nATOM ...\\nEND'}]</td></tr>
<tr><td style="word-wrap: break-word;">properties</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of property objects combined by Boltz List Combiner (e.g., affinity settings). Can be a single item or a list.</td><td style="word-wrap: break-word;">[{'affinity': {'target': 'A', 'ligand': 'L1'}}]</td></tr>
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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">Final Boltz YAML configuration dict with version, sequences, and optional constraints/templates/properties. Embedded content fields are replaced by generated filenames.</td><td style="word-wrap: break-word;">{'version': 1, 'sequences': [{'protein': {'id': 'A', 'sequence': 'MSEQNNTEMTF...', 'msa': 'msa_1.a3m'}}], 'templates': [{'pdb': 'template_2.pdb'}]}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Dictionary of auxiliary files referenced by boltz_yaml (e.g., msa_*.a3m, template_*.pdb/.cif). Keys are filenames; values are file contents.</td><td style="word-wrap: break-word;">{'msa_1.a3m': '>seqA\\nMSEQNNTEMTF...', 'template_2.pdb': 'HEADER\\nATOM ...\\nEND'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- All chain/entity IDs across sequences must be unique; duplicates cause an error.
- Supported entity types are protein, dna, rna, and ligand. Others are rejected.
- Protein/DNA/RNA entries must include id and sequence fields.
- Ligand entries must include exactly one of smiles or ccd (not both).
- MSA handling: if two proteins share the exact same sequence and both provide MSA content, a single shared MSA file is created and reused to meet Boltz requirements.
- MSA content labeled as empty or missing is skipped and not turned into a file.
- Template content provided as pdb or cif is converted into a generated file (template_N.pdb/.cif) and the YAML is updated to reference the filename.
- Inputs can be provided as a single object or a list; the node normalizes them internally.
- The node auto-sets YAML version to 1 if not provided.

## Troubleshooting
- Duplicate chain ID found: Ensure every chain/entity id is unique across all sequences.
- Invalid entity type: Only protein, dna, rna, or ligand are allowed.
- Missing 'id' or 'sequence': Protein/DNA/RNA entries must include both fields.
- Ligand field conflict: Provide exactly one of 'smiles' or 'ccd' for each ligand entry.
- At least one sequence is required: Confirm the sequences input is connected and non-empty.
- Template content missing or wrong field: Provide PDB content under 'pdb' or CIF content under 'cif'.
- MSA not applied: If MSA is 'empty' or omitted, no MSA file will be generated; provide valid MSA content if needed.
