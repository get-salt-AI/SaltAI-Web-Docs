# Load CIF

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node ingests a raw CIF or mmCIF text string and wraps it into a typed CIF structure object keyed by a user-defined identifier. The result is a dictionary of the form {cif_id: cif_string}, enriched with metadata via the AlphaFoldBaseNode base class so that biotech workflows can export and track results consistently. It is the CIF analogue of LoadPDBNode and is typically used as the starting point for structure-based pipelines that require mmCIF input.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/loadcifnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node whenever you need to bring an existing protein or complex structure in CIF or mmCIF format into a Salt biotech workflow. Typical scenarios include starting an AlphaFold or other structural analysis pipeline from an experimentally solved mmCIF file, feeding structures into downstream nodes that support the CIF type alongside PDB, or storing and later exporting structure batches using SaveToZipNode or SaveToBucketNode. Place Load CIF near the beginning of your workflow as the source of structural data. The cif_id you provide will be used as the key in the output dictionary, so it should be unique across all structures in the same workflow and should match any external FASTA or MSA identifiers where relevant. Upstream, the CIF string typically comes from a local file you open and paste, a script that fetches structures from a database, or another custom loader. Downstream, its output can be combined with other structural or sequence inputs for modeling, visualization, or export using nodes like SaveToZipNode, SaveToBucketNode, or any node that declares a CIF input. As a best practice, choose stable, descriptive cif_id values, such as 4HHB_A or designed_complex_v1, so that when you batch or export results you can easily trace each structure. If you plan to handle multiple structures, keep a clear naming convention and avoid reusing IDs to prevent collisions. Use this node alongside LoadFastaNode, LoadA3MNode, and LoadPDBNode to standardize how different biomolecular resources enter your pipeline.

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
<tr><td style="word-wrap: break-word;">cif_string</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Raw CIF or mmCIF file contents as a single text string. This should be the exact text of the .cif or .mmcif file, including the leading data_ block, loop_ sections, and all coordinate and metadata records. Empty or non-string values will pass through this node but will typically break or be rejected by downstream structure-processing nodes, so ensure the string contains valid structure data.</td><td style="word-wrap: break-word;">data_4HHB _entry.id 4HHB loop_ _atom_site.group_PDB _atom_site.id _atom_site.type_symbol _atom_site.label_atom_id _atom_site.label_comp_id _atom_site.label_asym_id _atom_site.label_seq_id _atom_site.Cartn_x _atom_site.Cartn_y _atom_site.Cartn_z ATOM 1 N N GLY A 1 11.104 8.678 2.345 ATOM 2 C CA GLY A 1 12.345 9.012 2.789 ...</td></tr>
<tr><td style="word-wrap: break-word;">cif_id</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Identifier key assigned to this CIF structure within the workflow. It becomes the dictionary key in the CIF output and should be unique among all structures you load. If you provide a corresponding FASTA or MSA for the same molecule, use the same ID to keep associations consistent. Avoid characters that could be ambiguous in file names if you plan to export, and do not reuse the same ID for different structures in the same run.</td><td style="word-wrap: break-word;">4HHB_chain_A</td></tr>
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
<tr><td style="word-wrap: break-word;">structure.cif</td><td style="word-wrap: break-word;">CIF</td><td style="word-wrap: break-word;">A CIF-typed structure object representing a dictionary that maps the user-provided cif_id to the CIF or mmCIF text. Internally it also carries metadata used by export and batch-processing nodes to decide folder and file naming. Downstream nodes should treat this as a mapping from structure ID to its CIF string when iterating or combining structures.</td><td style="word-wrap: break-word;">{ "4HHB_chain_A": "data_4HHB\n_entry.id 4HHB\nloop_\n_atom_site.group_PDB _atom_site.id _atom_site.type_symbol ..." }</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node is very lightweight; it does not parse, validate, or transform the CIF content, so execution time is essentially just wrapping the provided string into a CIF-typed container.
- **Limitations**: No structural or format validation is performed, so malformed or non-CIF text will still be accepted but may cause errors or misleading results in downstream nodes that parse the structure.
- **Behavior**: The output is always a single-element dictionary mapping cif_id to cif_string, wrapped with AlphaFoldBaseNode metadata; to manage multiple structures, instantiate multiple Load CIF nodes and ensure each uses a distinct cif_id.
- **Behavior**: If you reuse the same cif_id across different nodes and later merge their outputs, keys may silently overwrite each other; maintain unique, descriptive IDs and, when writing custom combiners, explicitly check for duplicate keys.

## Troubleshooting
- **Empty or missing structure**: If downstream nodes complain about empty or missing CIF data, verify that cif_string is not blank and that you pasted the full contents of the .cif or .mmcif file, including the data_ header and all coordinate records.
- **Unexpected key names**: If a later node that expects a particular structure ID cannot find it, check that cif_id exactly matches the identifier that node assumes, including casing, underscores, and any chain suffixes you use.
- **Colliding IDs in combined workflows**: If merging CIF outputs appears to drop or overwrite structures, make sure each Load CIF node uses a unique cif_id and that any combining logic warns or fails on duplicate keys instead of overwriting silently.
- **Downstream parse failures**: If a node that parses CIF content raises format or parsing errors, inspect cif_string for truncation, copy-paste artefacts, encoding issues, or accidental inclusion of non-CIF text such as logs or HTML markup.
