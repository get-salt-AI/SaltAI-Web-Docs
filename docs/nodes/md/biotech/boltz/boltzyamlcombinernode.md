# Boltz YAML Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines sequences, constraints, templates, and properties into a single Boltz YAML configuration and a companion files bundle. It validates structure, enforces unique chain IDs, and automatically converts embedded MSA/template content into referenced filenames while emitting those contents in the files bundle. Also ensures ligand fields are valid and sets a default YAML version.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzyamlcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after assembling inputs with the Boltz Sequence/Constraint/Template/Property builders and (optionally) Boltz List Combiner. Connect the combined sequences (required), and optionally constraints, templates, and properties. Feed this node’s outputs (boltz_yaml and boltz_files) directly into downstream Boltz execution nodes (e.g., prediction).

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
<tr><td style="word-wrap: break-word;">sequences</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">List of sequence objects produced by Boltz Sequence Builder nodes (often aggregated with Boltz List Combiner). Each item is a mapping like {'protein'\|'dna'\|'rna'\|'ligand': {...}} including 'id' and content fields.</td><td style="word-wrap: break-word;">[{"protein": {"id": "A", "sequence": "MKT...", "msa": "...a3m content...", "_msa_format": "a3m"}}, {"ligand": {"id": "L", "smiles": "CCO"}}]</td></tr>
<tr><td style="word-wrap: break-word;">constraints</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of constraint objects from Boltz Constraint Builder (e.g., pocket, contact, bond constraints).</td><td style="word-wrap: break-word;">[{"pocket": {"binder": "L", "contacts": [["A", 10], ["A", 25]], "max_distance": 6.0}}]</td></tr>
<tr><td style="word-wrap: break-word;">templates</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of template objects from Boltz Template Builder. Accepts PDB or CIF content that will be converted into referenced filenames.</td><td style="word-wrap: break-word;">[{"pdb": "HEADER...\nATOM ...\nEND"} ]</td></tr>
<tr><td style="word-wrap: break-word;">properties</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of property objects from Boltz Property Builder (e.g., affinity with a binder chain).</td><td style="word-wrap: break-word;">[{"affinity": {"binder": "L"}}]</td></tr>
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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">Validated Boltz YAML configuration containing sequences (and optional constraints/templates/properties). References MSAs/templates by generated filenames.</td><td style="word-wrap: break-word;">{"version": 1, "sequences": [{"protein": {"id": "A", "sequence": "MKT...", "msa": "msa_1.a3m"}}, {"ligand": {"id": "L", "smiles": "CCO"}}], "templates": [{"pdb": "template_2.pdb"}], "constraints": [{"pocket": {"binder": "L", "contacts": [["A", 10], ["A", 25]], "max_distance": 6.0}}]}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Auxiliary files dictionary containing emitted MSA and template file contents keyed by the filenames referenced in boltz_yaml.</td><td style="word-wrap: break-word;">{"msa_1.a3m": "...a3m content...", "template_2.pdb": "HEADER...\nATOM ...\nEND"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Sequences are required; constraints, templates, and properties are optional.
- All chain IDs across all sequences (including multiple copies) must be unique; duplicates cause validation errors.
- For ligands, provide either 'smiles' or 'ccd' (but not both).
- Protein MSAs may be supplied inline; the node will generate filenames (e.g., msa_1.a3m) and move contents into boltz_files. Identical protein sequences will automatically share the same MSA file reference.
- Templates supplied as PDB or CIF content are converted to filenames (template_X.pdb or template_X.cif) and stored in boltz_files.
- The node sets 'version' to 1 if not specified.

## Troubleshooting
- Duplicate chain ID found: Ensure every chain ID across all sequences is unique (including additional copies defined via multiple chains).
- Missing sequence field: For protein/dna/rna entries, include a non-empty 'sequence'.
- Invalid ligand specification: Provide exactly one of 'smiles' or 'ccd' for ligand entries.
- No sequences provided: Supply at least one sequence object via the sequences input (commonly a list from Boltz List Combiner).
- Template or MSA not appearing in files: Confirm content is non-empty; the node only emits file entries for provided contents.
- MSA/sequence mismatch warnings: If identical sequences are present, the node reuses a single MSA file reference to satisfy tool requirements. Review your configuration if unexpected sharing occurs.
