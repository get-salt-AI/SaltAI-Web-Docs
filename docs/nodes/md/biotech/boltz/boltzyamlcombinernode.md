# Boltz YAML Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines one or more prepared Boltz components (sequences, constraints, templates, properties) into a single Boltz YAML configuration ready for prediction. It also packages auxiliary file contents (e.g., MSA and template structures) into a files dictionary and replaces in-YAML inline contents with generated file references.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzyamlcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after building components with Boltz Sequence/Constraint/Template/Property builders and optionally combining them with Boltz List Combiner. Connect the required sequences input, and optionally constraints, templates, and properties. The node outputs a validated Boltz YAML object plus a dictionary of auxiliary files that downstream Boltz nodes will consume.

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
<tr><td style="word-wrap: break-word;">sequences</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">List of sequence objects (proteins, nucleic acids, ligands) assembled upstream. Each sequence must include a unique chain id and required fields (e.g., 'sequence' for proteins/DNA/RNA, or 'smiles'/'ccd' for ligands).</td><td style="word-wrap: break-word;">[{"protein": {"id": "A", "sequence": "MKT...", "msa": "...a3m content...", "_sequence_name": "seqA"}}]</td></tr>
<tr><td style="word-wrap: break-word;">constraints</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of constraint objects (e.g., bond, pocket, contact) prepared upstream.</td><td style="word-wrap: break-word;">[{"pocket": {"binder": "L", "contacts": [["A", 10], ["A", 25]], "max_distance": 8.0}}]</td></tr>
<tr><td style="word-wrap: break-word;">templates</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of template objects containing structure content (e.g., PDB text). These will be converted into file references and stored in the auxiliary files output.</td><td style="word-wrap: break-word;">[{"pdb": "ATOM ...\nEND"}]</td></tr>
<tr><td style="word-wrap: break-word;">properties</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of property requests (currently affinity) to compute during prediction.</td><td style="word-wrap: break-word;">[{"affinity": {"binder": "L"}}]</td></tr>
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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">A validated Boltz YAML data structure containing version, sequences, and optional constraints/templates/properties. Any inline MSA or template contents will be replaced with generated filenames.</td><td style="word-wrap: break-word;">{"version": 1, "sequences": [{"protein": {"id": "A", "sequence": "MKT...", "msa": "msa_1.a3m"}}], "templates": [{"pdb": "template_2.pdb"}]}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Dictionary of auxiliary files referenced by boltz_yaml (e.g., msa_*.a3m, template_*.pdb). Keys are filenames and values are the corresponding file contents.</td><td style="word-wrap: break-word;">{"msa_1.a3m": ">seq\nMKT...\n", "template_2.pdb": "ATOM ...\nEND"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Unique chain IDs**: All chain IDs across all sequences must be unique. The node will fail if duplicates are found.
- **Required fields**: Protein/DNA/RNA entries must include 'id' and 'sequence'. Ligands must include either 'smiles' or 'ccd' (but not both).
- **MSA handling**: If a protein has an MSA and it is not 'empty', the content is written to an a3m file and the YAML references that filename. Identical protein sequences share a single MSA file automatically.
- **Template handling**: Template structures (PDB/CIF content) are converted to files (e.g., template_1.pdb) and the YAML references those filenames.
- **Deep-copy safety**: Inputs are cloned internally to avoid mutating cached outputs from upstream nodes.
- **Versioning**: If not present, 'version' is set to 1 in the output YAML.

## Troubleshooting
- **Error: Duplicate chain ID found**: Ensure every sequence specifies a unique 'id'. If you duplicated a chain via multiple-chains options upstream, verify there are no overlaps with other entities.
- **Error: At least one sequence is required**: Connect a valid sequences list from upstream. The node cannot produce YAML without sequences.
- **Error: Missing 'sequence' field**: For protein/DNA/RNA entries, provide a non-empty 'sequence'. If using FASTA upstream, make sure it was parsed into the sequence field.
- **Error: Ligand must have 'smiles' or 'ccd'**: Provide exactly one of these fields for ligand entries; remove the other.
- **Unexpected empty auxiliary files**: If an MSA is set to 'empty', no MSA file is generated and the YAML may omit or retain 'msa' as 'empty'. Provide valid A3M content upstream if you need an MSA file.
- **Template not saved**: Ensure the template object contains valid structure content under 'pdb' (or 'cif' for compatibility). Empty content will be rejected.
