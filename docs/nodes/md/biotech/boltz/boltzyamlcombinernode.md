# Boltz YAML Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines molecular building blocks (sequences, constraints, templates, properties) into a single Boltz YAML configuration and a companion set of auxiliary files. It validates structure and uniqueness of chain IDs, converts inline MSA/template contents into file references, and prepares everything needed for downstream Boltz prediction or partial diffusion.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzyamlcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after assembling lists of sequences and optional constraints, templates, and properties (typically via the Boltz List Combiner). Connect the resulting YAML and files to Boltz prediction or partial diffusion nodes. This is the central step that turns individual components into a valid, ready-to-run Boltz configuration.

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
<tr><td style="word-wrap: break-word;">sequences</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">List of sequence objects (proteins, nucleic acids, ligands) defining chain IDs and content. Must include at least one sequence. Chain IDs across all sequences must be unique.</td><td style="word-wrap: break-word;">[{"protein": {"id": "A", "sequence": "MPEPTIDE...", "msa": "empty", "_sequence_name": "protA"}}, {"ligand": {"id": "L", "smiles": "CCO"}}]</td></tr>
<tr><td style="word-wrap: break-word;">constraints</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of constraint objects (e.g., pocket, bond, contact) to guide modeling.</td><td style="word-wrap: break-word;">[{"pocket": {"binder": "L", "contacts": [["A", 10], ["A", 25]], "max_distance": 8.0}}]</td></tr>
<tr><td style="word-wrap: break-word;">templates</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of template objects. Inline PDB/CIF content will be extracted into files and replaced with filenames in the YAML.</td><td style="word-wrap: break-word;">[{"pdb": "ATOM ...\nEND"}]</td></tr>
<tr><td style="word-wrap: break-word;">properties</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of properties to predict, such as affinity.</td><td style="word-wrap: break-word;">[{"affinity": {"binder": "L"}}]</td></tr>
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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">Validated Boltz YAML configuration dictionary that references external MSA/template files where applicable.</td><td style="word-wrap: break-word;">{"version": 1, "sequences": [{"protein": {"id": "A", "sequence": "MPEPTIDE...", "msa": "msa_1.a3m"}}, {"ligand": {"id": "L", "smiles": "CCO"}}], "templates": [{"pdb": "template_2.pdb"}], "constraints": [{"pocket": {"binder": "L", "contacts": [["A", 10], ["A", 25]]}}]}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Dictionary mapping generated filenames to their file contents (e.g., MSA .a3m files and template .pdb/.cif files).</td><td style="word-wrap: break-word;">{"msa_1.a3m": ">seq\nMPEPTIDE...\n", "template_2.pdb": "ATOM ...\nEND\n"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Chain ID uniqueness**: All chain IDs across all sequences must be unique. Duplicate IDs cause an error.
- **YAML structure validation**: The node ensures a valid Boltz format. The 'version' field is set to 1 if missing, and at least one sequence is required.
- **Ligand specification**: Each ligand must contain either 'smiles' or 'ccd', but not both.
- **MSA handling**: For protein sequences, if 'msa' is provided and not 'empty', the content is saved as a .a3m file and the YAML references the filename. If two protein chains have identical sequences with MSAs, a single shared MSA file is reused.
- **Template handling**: Inline PDB/CIF in templates is written to template files and replaced with filenames in the YAML.
- **Input normalization**: Sequences are required; constraints, templates, and properties are optional and can be provided as individual items or lists.

## Troubleshooting
- **Error: Duplicate chain ID found**: Ensure each sequence's 'id' values are unique across all proteins, nucleic acids, and ligands (including when 'id' is a list for multiple chains).
- **Error: At least one sequence is required**: Provide a non-empty 'sequences' list.
- **Error: Missing 'sequence' for protein/dna/rna**: Supply a 'sequence' field for each non-ligand entity.
- **Error: Ligand must have 'smiles' or 'ccd' only**: Provide exactly one of these fields for each ligand.
- **MSA not applied**: If 'msa' is 'empty' or blank, no MSA file is created. Provide valid A3M content to generate a file.
- **Template not referenced**: Ensure template objects include valid PDB/CIF content; otherwise, no template file will be produced.
- **Unexpected input type**: Inputs should be lists/dicts matching the expected structure. If coming from other nodes, connect via the Boltz List Combiner to ensure proper formatting.
