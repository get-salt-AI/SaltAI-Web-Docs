# Boltz YAML Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines molecular sequences, constraints, templates, and properties into a single Boltz YAML configuration plus auxiliary files. It validates structure and uniqueness of chain IDs, rewrites inline MSA/template contents into file references, and prepares outputs suitable for Boltz prediction and partial diffusion workflows.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/BoltzYAMLCombinerNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after building inputs with Boltz Sequence/Constraint/Template/Property nodes and optionally aggregating them with Boltz List Combiner. Connect the resulting boltz_yaml and boltz_files to Boltz Predict or Boltz Partial Diffusion to run structure generation or refinement. Ideal when assembling multimers, protein–ligand systems, or models with constraints/templates.

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
<tr><td style="word-wrap: break-word;">sequences</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">One or more sequence objects describing proteins/DNA/RNA/ligands, typically produced by Boltz Sequence nodes and combined with Boltz List Combiner. Each entry must include chain id(s) and required fields (e.g., sequence for biopolymers; smiles or ccd for ligands).</td><td style="word-wrap: break-word;">[{'protein': {'id': 'A', 'sequence': 'MAA...', 'msa': 'empty'}}, {'ligand': {'id': 'L', 'smiles': 'CCO'}}]</td></tr>
<tr><td style="word-wrap: break-word;">constraints</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of constraints (e.g., pocket, bond, contact) from Boltz Constraint Builder, usually passed via Boltz List Combiner.</td><td style="word-wrap: break-word;">[{'pocket': {'binder': 'L', 'contacts': [['A', 10], ['A', 25]]}}]</td></tr>
<tr><td style="word-wrap: break-word;">templates</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of template objects with structure content (e.g., PDB). The node will convert inline structure content into file references in the YAML and place the actual content in boltz_files.</td><td style="word-wrap: break-word;">[{'pdb': '<PDB file content>', 'chain_id': 'A'}]</td></tr>
<tr><td style="word-wrap: break-word;">properties</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of properties to compute (e.g., affinity), typically from Boltz Property Builder.</td><td style="word-wrap: break-word;">[{'affinity': {'binder': 'L'}}]</td></tr>
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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">Structured Boltz YAML data (as an object) containing version, sequences, and optional constraints/templates/properties. MSA/template entries point to filenames present in boltz_files.</td><td style="word-wrap: break-word;">{'version': 1, 'sequences': [{'protein': {'id': 'A', 'sequence': 'MAA...', 'msa': 'msa_1.a3m'}}, {'ligand': {'id': 'L', 'smiles': 'CCO'}}], 'templates': [{'pdb': 'template_1.pdb'}], 'properties': [{'affinity': {'binder': 'L'}}]}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Auxiliary files referenced by boltz_yaml (e.g., MSA .a3m files and template .pdb). Keys are filenames; values are file contents.</td><td style="word-wrap: break-word;">{'msa_1.a3m': '>seq\nMAA...', 'template_1.pdb': 'ATOM ...'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- All chain IDs across all sequences must be unique; duplicates cause an error.
- At least one sequence is required. Proteins/DNA/RNA must include a sequence; ligands must include either smiles or ccd (but not both).
- MSA handling: 'empty' is allowed and does not create a file. If multiple protein chains share the exact same sequence, a single shared MSA file is created and referenced by all such chains.
- Template structure content is moved to external files (e.g., template_#.pdb) and the YAML references those filenames.
- Version is set to 1 automatically if not provided.
- Outputs are designed to feed directly into Boltz Predict or Boltz Partial Diffusion nodes.

## Troubleshooting
- Duplicate chain ID found: Modify chain_id or multiple_chains in your sequence inputs so that all chain IDs are unique.
- Missing required sequence fields: Ensure each protein/DNA/RNA entry has a sequence and each ligand has exactly one of smiles or ccd.
- Invalid ligand specification (both smiles and ccd): Provide only one field for ligand specification.
- Empty or invalid template content: Verify that the provided structure content (e.g., PDB) is non-empty and correctly passed to the Template Builder.
- Unexpected input format: Use Boltz List Combiner to aggregate multiple items; this node accepts either a single object or a list, but each item must follow the expected schema from the builder nodes.
- Downstream errors in prediction: Ensure that MSA filenames and template filenames output in boltz_yaml are present in boltz_files and that constraints/properties reference valid chain IDs.
