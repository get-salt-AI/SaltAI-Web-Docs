# Boltz YAML Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines sequences, constraints, templates, and properties into a single Boltz YAML configuration plus a companion files bundle. It validates the combined structure, enforces unique chain IDs, converts inline MSA and template contents into referenced filenames, ensures ligand fields are correctly specified, and sets a default YAML version when missing.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzyamlcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node after constructing Boltz components with the Sequence, Constraint, Template, and Property builder nodes, optionally merging lists with Boltz List Combiner. Provide at least one list of sequences (required) and any optional lists of constraints, templates, and properties. Then feed its boltz_yaml and boltz_files outputs directly into downstream Boltz execution nodes as the complete job specification.

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
<tr><td style="word-wrap: break-word;">sequences</td><td>True</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">List of sequence objects from Boltz Sequence Builder nodes, often aggregated via Boltz List Combiner. Each entry is a mapping keyed by 'protein', 'dna', 'rna', or 'ligand', containing an 'id' plus sequence or ligand information, and optionally MSA-related fields.</td><td style="word-wrap: break-word;">[{"protein": {"id": "A", "sequence": "MKT...", "msa": "...a3m content...", "_msa_format": "a3m"}}, {"ligand": {"id": "L", "smiles": "CCO"}}]</td></tr>
<tr><td style="word-wrap: break-word;">constraints</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of constraint objects from Boltz Constraint Builder. These specify geometric or interaction constraints (e.g., pockets, contacts, bonds) that reference chain IDs and residue indices.</td><td style="word-wrap: break-word;">[{"pocket": {"binder": "L", "contacts": [["A", 10], ["A", 25]], "max_distance": 6.0}}]</td></tr>
<tr><td style="word-wrap: break-word;">templates</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of template objects from Boltz Template Builder. Accepts inline PDB or CIF structural content; this node converts that content into generated filenames and stores the actual text in the files bundle.</td><td style="word-wrap: break-word;">[{"pdb": "HEADER...\nATOM ...\nEND"}]</td></tr>
<tr><td style="word-wrap: break-word;">properties</td><td>False</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">Optional list of property objects from Boltz Property Builder, such as affinity definitions that reference binder chains or other design-related properties.</td><td style="word-wrap: break-word;">[{"affinity": {"binder": "L"}}]</td></tr>
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
<tr><td style="word-wrap: break-word;">boltz_yaml</td><td style="word-wrap: break-word;">BOLTZ_YAML</td><td style="word-wrap: break-word;">Validated Boltz YAML configuration unifying sequences and any provided constraints, templates, and properties. Inline MSAs and templates are replaced by generated filenames that match keys in boltz_files, and a YAML version (default 1) is guaranteed.</td><td style="word-wrap: break-word;">{"version": 1, "sequences": [{"protein": {"id": "A", "sequence": "MKT...", "msa": "msa_1.a3m"}}, {"ligand": {"id": "L", "smiles": "CCO"}}], "templates": [{"pdb": "template_2.pdb"}], "constraints": [{"pocket": {"binder": "L", "contacts": [["A", 10], ["A", 25]], "max_distance": 6.0}}]}</td></tr>
<tr><td style="word-wrap: break-word;">boltz_files</td><td style="word-wrap: break-word;">BOLTZ_FILES</td><td style="word-wrap: break-word;">Auxiliary files dictionary mapping generated filenames (for MSAs, templates, and similar artifacts) to their contents, referenced by boltz_yaml. This bundle must be supplied alongside boltz_yaml for a complete Boltz job.</td><td style="word-wrap: break-word;">{"msa_1.a3m": "...a3m content...", "template_2.pdb": "HEADER...\nATOM ...\nEND"}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Note 1**: The sequences input is mandatory; at least one valid sequence object must be provided or the node will fail.
- **Note 2**: All chain IDs must be globally unique across all sequences, including repeated chains; duplicates cause validation errors.
- **Note 3**: Ligand entries must specify exactly one of 'smiles' or 'ccd'; providing both or neither is invalid.
- **Note 4**: Inline protein MSAs are extracted into separate files and referenced by filename; identical protein sequences may automatically share a single MSA file reference.
- **Note 5**: Template structures supplied as PDB or CIF text are removed from the YAML and replaced by generated filenames, with their raw contents stored only in boltz_files.
- **Note 6**: If no version is provided, the node sets 'version' to 1 in the resulting YAML configuration.
- **Note 7**: Inputs should be generated by official Boltz builder nodes; arbitrary JSON structures that do not follow their formats may fail validation or yield unusable configurations.

## Troubleshooting
- **Duplicate chain ID found**: Check all sequence entries and ensure each 'id' is unique, including any additional copies of the same molecule; rename conflicting IDs and recombine.
- **No sequences provided**: Connect a non-empty list of sequence objects, typically via Boltz List Combiner, to the sequences input before executing this node.
- **Missing or empty sequence field**: For protein, DNA, or RNA items, verify that a non-empty 'sequence' string is present; regenerate or correct these entries in the Sequence Builder.
- **Invalid ligand specification**: Inspect ligand entries to confirm that exactly one of 'smiles' or 'ccd' is set; remove any extra field or add the missing one.
- **Templates or MSAs not appearing in boltz_files**: Ensure that inline template or MSA content is actually provided and non-empty; the node only emits file entries for valid content.
- **Unexpected MSA sharing between chains**: Identical protein sequences may share a single MSA file reference by design; if this is undesirable, modify sequence definitions or MSA assignment.
- **Downstream node rejects configuration**: Confirm that both boltz_yaml and boltz_files are passed together and that every filename referenced in boltz_yaml exists as a key in boltz_files.
