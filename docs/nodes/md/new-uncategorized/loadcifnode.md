# Load CIF

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node ingests raw CIF/mmCIF text and wraps it into a structured CIF object for Salt biotech workflows. It returns a dictionary mapping a user-specified cif_id to the provided CIF string, enabling downstream structure-processing nodes to reference the structure by ID. The content is passed through unchanged; the node’s primary role is packaging and standardizing structure input.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/loadcifnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use Load CIF at the beginning of a biotech workflow when you already have a protein or nucleic-acid structure in CIF/mmCIF format and need to bring it into Salt for further processing. Provide the complete CIF text and assign a unique cif_id; this ID is how downstream nodes will refer to the structure, and it should align with any related sequence IDs (for example, in FASTA) when a paired structure–sequence workflow is expected. Typically, this node sits upstream of structure analysis, visualization, comparison, or export utilities that accept CIF-typed inputs, and it is often used alongside loaders such as Load FASTA and Load A3M when building an end-to-end structural modeling pipeline. Best practices: (1) ensure cif_id values are unique across all structures in the same workflow, (2) keep the CIF text complete and unmodified (no truncated headers or removed data_ blocks), and (3) when using multiple structures, use descriptive IDs such as "apo_state", "ligand_bound", or "mutant_A42G" so downstream results remain interpretable.

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
<tr><td style="word-wrap: break-word;">cif_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Full CIF/mmCIF file content as plain text. It should include the leading data_ block and all necessary loops and tags for a valid mmCIF file. Multiline input is supported; paste or load the entire file content. While this node accepts any string, empty or malformed CIF text will generally cause failures or incorrect behavior in downstream structure-processing nodes.</td><td style="word-wrap: break-word;">data_5XNL # _entry.id 5XNL # _loop _atom_site.group_PDB _atom_site.id _atom_site.type_symbol _atom_site.label_atom_id _atom_site.label_comp_id _atom_site.label_asym_id _atom_site.Cartn_x _atom_site.Cartn_y _atom_site.Cartn_z ATOM 1 N N MET A 12.345 23.456 34.567 ATOM 2 C CA MET A 13.111 24.222 35.333 #</td></tr>
<tr><td style="word-wrap: break-word;">cif_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Logical identifier assigned to this CIF structure within the workflow. Must be a non-empty string and should be unique across all structures in the pipeline. If pairing this structure with a sequence (for example, from Load FASTA), use a cif_id that matches the relevant sequence header or logical sequence ID so downstream nodes can correlate them reliably.</td><td style="word-wrap: break-word;">5XNL_chainA_model1</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.cif</td><td style="word-wrap: break-word;">CIF</td><td style="word-wrap: break-word;">A CIF-typed dictionary where the key is the provided cif_id and the value is the raw CIF/mmCIF text. The exact data structure is {cif_id: cif_string}. This packaged object is what downstream biotech nodes expect when they require a CIF structure input for operations like structure analysis, comparison, or saving to disk or cloud storage.</td><td style="word-wrap: break-word;">{'5XNL_chainA_model1': 'data_5XNL\n#\n_entry.id 5XNL\n#\n_loop\n_atom_site.group_PDB\n_atom_site.id\n_atom_site.type_symbol\n_atom_site.label_atom_id\n_atom_site.label_comp_id\n_atom_site.label_asym_id\n_atom_site.Cartn_x\n_atom_site.Cartn_y\n_atom_site.Cartn_z\nATOM 1 N N MET A 12.345 23.456 34.567\nATOM 2 C CA MET A 13.111 24.222 35.333\n#'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node is lightweight and essentially just wraps the provided text into a CIF-typed container and attaches metadata; performance considerations mostly relate to the size of the CIF string you provide.
- **Limitations**: Load CIF does not validate mmCIF syntax or structural correctness. If the content is malformed, errors will appear only when downstream nodes try to parse or operate on the structure.
- **Behavior**: Output is always a mapping from cif_id to cif_string, even if only one structure is present. Downstream nodes consuming CIF expect this dictionary-shaped structure, not a bare string.
- **Behavior**: If multiple CIF dictionaries are combined later and share the same cif_id, the last one written may overwrite previous entries. Plan unique, descriptive cif_id values to avoid silent collisions.

## Troubleshooting
- **Empty or missing structure in later nodes**: If a downstream node reports that no structure was received, confirm that cif_string is not empty and that you pasted the full mmCIF file content, including the initial data_ line and atom_site loop.
- **ID-related lookup failures**: When another node cannot find the expected structure by ID, check that the cif_id configured here exactly matches what the downstream node expects, including case, chain suffixes, and any model or variant tags.
- **CIF parsing or format errors downstream**: If a later step fails with mmCIF parsing errors, Load CIF is functioning correctly but the mmCIF text itself is invalid or corrupted. Re-export the structure from your modeling tool in mmCIF format, avoid editing structural tags manually, and ensure that the text was not truncated during copy and paste.
- **Unexpected dictionary shape in custom logic**: If you attach custom or generic nodes after Load CIF and see confusing data structures, remember that the output is {cif_id: cif_string}, not just a raw string. Adjust any custom processing to first select the appropriate value from the dictionary by its key.
