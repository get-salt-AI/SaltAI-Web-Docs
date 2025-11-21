# Boltz YAML Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines sequences, constraints, templates, and properties into a single Boltz YAML configuration and assembles all auxiliary files referenced by that YAML. It validates chain ID uniqueness and schema requirements, rewrites inlined MSA/template contents to file references, and returns both the YAML object and a keyed map of file contents.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzyamlcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after building lists of items with the Boltz Sequence/Protein/Ligand, Constraint, Template, and Property builder nodes (optionally merged via Boltz List Combiner). Connect the resulting lists into this node to generate a valid Boltz YAML and associated files. Feed its outputs into Boltz Predict or Boltz Partial Diffusion to run inference.

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
<tr><td style="word-wrap: break-word;">sequences</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">List (or single item) of sequence objects describing proteins, DNA/RNA, or ligands. Typically produced by Boltz Sequence Builder or its specialized variants and merged via Boltz List Combiner.</td><td style="word-wrap: break-word;">[{"protein": {"id": "A", "sequence": "MKT...", "msa": "empty", "_sequence_name": "seqA"}}, {"ligand": {"id": "L", "smiles": "CCO"}}]</td></tr>
<tr><td style="word-wrap: break-word;">constraints</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">List (or single item) of constraint objects (bond, pocket, contact) to guide modeling. Optional; typically from Boltz Constraint Builder combined via Boltz List Combiner.</td><td style="word-wrap: break-word;">[{"pocket": {"binder": "L", "contacts": [["A", 10], ["A", 15]], "max_distance": 6.0}}]</td></tr>
<tr><td style="word-wrap: break-word;">templates</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">List (or single item) of template objects with structure content. Optional; typically from Boltz Template Builder combined via Boltz List Combiner.</td><td style="word-wrap: break-word;">[{"pdb": "ATOM ...\nEND"}]</td></tr>
<tr><td style="word-wrap: break-word;">properties</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">List (or single item) of property requests, e.g., affinity prediction specifications. Optional; typically from Boltz Property Builder.</td><td style="word-wrap: break-word;">[{"affinity": {"binder": "L"}}]</td></tr>
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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">The assembled Boltz YAML configuration as a dictionary object, including sequences and optional constraints/templates/properties. MSA and template fields are converted to file references.</td><td style="word-wrap: break-word;">{"version": 1, "sequences": [{"protein": {"id": "A", "sequence": "MKT...", "msa": "msa_1.a3m", "_sequence_name": "seqA"}}, {"ligand": {"id": "L", "smiles": "CCO"}}], "templates": [{"pdb": "template_2.pdb"}], "properties": [{"affinity": {"binder": "L"}}]}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">A map of auxiliary filenames to their contents. Includes generated MSA files (e.g., msa_1.a3m) and template files (e.g., template_2.pdb or template_3.cif).</td><td style="word-wrap: break-word;">{"msa_1.a3m": ">seqA\nMKT...", "template_2.pdb": "ATOM ...\nEND"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Chain ID uniqueness**: All chain IDs across all sequences must be unique. The node will raise an error if duplicates are detected.
- **Schema validation**: Ensures version is set (defaults to 1) and that each sequence has required fields (id and sequence for protein/dna/rna; either smiles or ccd for ligands, but not both).
- **MSA handling**: For protein sequences with non-empty MSA content, the node writes MSA contents to generated filenames (msa_X.a3m) and replaces inline content with the filename. If multiple chains have identical sequences, they share a single MSA file.
- **Template handling**: Template PDB/CIF contents are written to generated filenames (template_X.pdb or template_X.cif) and the template entries are updated to reference those filenames.
- **Flexible inputs**: Each input can be a single object or a list; the node normalizes to lists internally.
- **Intended pipeline**: Outputs should be passed to Boltz Predict or Boltz Partial Diffusion nodes.

## Troubleshooting
- **Error: Duplicate chain ID found**: Ensure each chain ID across all sequences is unique. If duplicating a chain, use different IDs or configure multiple_chains appropriately when building sequences.
- **Error: At least one sequence is required**: Provide at least one sequence object as input.
- **Error: Missing 'sequence' field in protein/dna/rna**: Verify sequence builders produced objects containing 'sequence' and 'id'.
- **Error: Ligand must have either 'smiles' or 'ccd' (not both)**: Provide only one of these fields in ligand entries.
- **MSA not applied as expected**: Confirm the protein entry's 'msa' is not 'empty' and contains valid A3M content. The node will convert content to a file reference in the YAML and add the file into boltz_files.
- **Templates not referenced**: Ensure each template object includes 'pdb' or legacy 'cif' content. The node will create a corresponding file and update the YAML reference.
- **Unexpected list vs single item behavior**: If providing a single object instead of a list, the node will wrap it to a list automatically. Use Boltz List Combiner to merge multiple sources consistently.
