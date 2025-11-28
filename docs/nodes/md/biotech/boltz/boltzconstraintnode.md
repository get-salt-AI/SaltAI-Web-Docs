# Boltz Constraint Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates one or more structural constraints for Boltz YAML configurations. Supports three constraint types: bond (between specific atoms), pocket (binder-to-pocket residue contacts), and contact (pairwise residue contacts). Inputs accept multiple entries, enabling batch creation of constraints in a single node invocation.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzconstraintnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define geometric priors before running a Boltz prediction. Connect its output to a list combiner (if aggregating with other constraints) and then to the Boltz YAML combiner alongside sequences/templates/properties. Choose the constraint type, provide the appropriate token formats, and optionally set a maximum distance and a force flag to strengthen enforcement.

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
<tr><td style="word-wrap: break-word;">constraint_type</td><td>True</td><td style="word-wrap: break-word;">['bond', 'pocket', 'contact']</td><td style="word-wrap: break-word;">Select what kind of constraint to build. Bond links specific atoms; pocket defines a binder chain and target pocket residues; contact enforces proximity between residue pairs.</td><td style="word-wrap: break-word;">pocket</td></tr>
<tr><td style="word-wrap: break-word;">bond_atom1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For bond constraints: first atom(s) as semicolon-separated entries of chain_id,residue_idx,atom_name. Must match the number of entries in bond_atom2.</td><td style="word-wrap: break-word;">A,45,CA;B,10,N</td></tr>
<tr><td style="word-wrap: break-word;">bond_atom2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For bond constraints: second atom(s) as semicolon-separated entries of chain_id,residue_idx,atom_name. Must match the number of entries in bond_atom1.</td><td style="word-wrap: break-word;">A,46,C;B,11,CA</td></tr>
<tr><td style="word-wrap: break-word;">pocket_binder</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For pocket constraints: the binder chain ID.</td><td style="word-wrap: break-word;">L</td></tr>
<tr><td style="word-wrap: break-word;">pocket_contacts</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For pocket constraints: newline-separated residues that define the receptor pocket, each as chain_id,residue_idx (one per line).</td><td style="word-wrap: break-word;">A,100 A,105 B,22</td></tr>
<tr><td style="word-wrap: break-word;">contact_token1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For contact constraints: first residue token(s) as semicolon-separated entries of chain_id,residue_idx. Must match the number of entries in contact_token2.</td><td style="word-wrap: break-word;">A,10;A,25;B,30</td></tr>
<tr><td style="word-wrap: break-word;">contact_token2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For contact constraints: second residue token(s) as semicolon-separated entries of chain_id,residue_idx. Must match the number of entries in contact_token1.</td><td style="word-wrap: break-word;">B,15;C,40;D,5</td></tr>
<tr><td style="word-wrap: break-word;">max_distance</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Optional maximum distance threshold in Å. Applies to pocket and contact constraints only. Use 0 for no limit.</td><td style="word-wrap: break-word;">8.0</td></tr>
<tr><td style="word-wrap: break-word;">force</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, mark the constraint to be enforced with a potential. Applies to pocket and contact constraints.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">constraints</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list of constraint objects ready to be combined into a Boltz YAML configuration.</td><td style="word-wrap: break-word;">[{"pocket": {"binder": "L", "contacts": [["A", 100], ["A", 105]], "max_distance": 8.0, "force": true}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Formatting matters**: bond_atom1/bond_atom2 entries use semicolons to separate pairs; each entry is chain_id,residue_idx,atom_name.
- **Contact pairs must match**: contact_token1 and contact_token2 must contain the same number of semicolon-separated entries.
- **Pocket contacts are newline-separated**: each line is chain_id,residue_idx.
- **max_distance and force**: only applied to pocket and contact constraints; they are ignored for bond constraints.
- **Batch creation**: Multiple constraints can be created at once by supplying multiple entries (semicolon-separated for bond/contact; newline-separated for pocket contacts).
- **Validation**: The node will raise errors for empty required fields, mismatched entry counts, or malformed tokens.

## Troubleshooting
- **Error: Both bond_atom1 and bond_atom2 are required**: Provide both fields with the same number of entries, formatted as chain_id,residue_idx,atom_name.
- **Error: Binder and contacts are required for pocket constraints**: Set pocket_binder and at least one valid line in pocket_contacts.
- **Error: Both contact_token1 and contact_token2 are required**: Provide both fields with semicolon-separated entries and ensure the counts match.
- **Invalid format (expected chain_id,residue_idx[,atom_name])**: Check delimiters; use commas within an entry, semicolons to separate entries (bond/contact), and newlines for pocket contacts. Residue indices must be integers.
- **No valid constraints found for type**: Ensure the selected constraint_type has the necessary inputs populated and correctly formatted.
- **Unexpected application of max_distance/force**: Remember these settings do not affect bond constraints.
