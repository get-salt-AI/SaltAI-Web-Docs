# Boltz Constraint Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node builds constraint objects for the Boltz YAML format, supporting bond, pocket, and contact constraints. It parses user-friendly string descriptions of residues and atoms into validated, structured constraint dictionaries that Boltz can consume. Multiple constraint pairs can be defined in a single node using semicolon- or newline-separated formats, enabling compact specification of complex restraint sets.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzconstraintnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to steer a Boltz prediction with geometric constraints such as specific atom–atom bonds, ligand binding pockets, or residue–residue contacts. It typically sits downstream of sequence/template builders (e.g., Boltz Sequence Builder, Boltz Protein Sequence, Boltz Ligand Sequence, Boltz Template Builder) and upstream of Boltz List Combiner and Boltz YAML Combiner, which aggregate constraints with sequences/templates/properties before invoking Boltz Predict. A common workflow is: build sequences → optionally build constraints with this node (one or more times) and templates/properties → merge all objects with Boltz List Combiner → assemble into a full YAML and auxiliary file map using Boltz YAML Combiner → run Boltz Predict. For bond constraints, specify pairs of atoms by chain, residue index, and atom name; for pocket constraints, define a binder chain and a set of receptor residues; for contact constraints, define residue–residue pairs. Prefer using a separate Boltz Constraint Builder per logical constraint set (e.g., one node for pocket, one for contacts) and then combine them via Boltz List Combiner for better organization.

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
<tr><td style="word-wrap: break-word;">constraint_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE['bond','pocket','contact']</td><td style="word-wrap: break-word;">Selects which kind of constraint to construct. 'bond' creates atom–atom bond constraints, 'pocket' defines a binding pocket between a binder chain and specific residues, and 'contact' defines residue–residue distance constraints. The other text fields must be completed according to the selected type.</td><td style="word-wrap: break-word;">pocket</td></tr>
<tr><td style="word-wrap: break-word;">bond_atom1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For bond constraints: semicolon-separated list of first atoms in each pair, formatted as chain_id,residue_idx,atom_name. The parser expects exactly three comma-separated parts per entry, and residue_idx must be an integer. The number of entries must match bond_atom2.</td><td style="word-wrap: break-word;">A,45,N;A,60,CA</td></tr>
<tr><td style="word-wrap: break-word;">bond_atom2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For bond constraints: semicolon-separated list of second atoms in each pair, formatted as chain_id,residue_idx,atom_name. Must contain the same number of valid entries as bond_atom1; each residue index must be an integer.</td><td style="word-wrap: break-word;">B,10,C;B,25,CB</td></tr>
<tr><td style="word-wrap: break-word;">pocket_binder</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For pocket constraints: chain ID of the binder (typically a ligand or peptide) whose position relative to the pocket residues will be constrained. Required when constraint_type is 'pocket'.</td><td style="word-wrap: break-word;">L</td></tr>
<tr><td style="word-wrap: break-word;">pocket_contacts</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For pocket constraints: newline-separated list of receptor residues that define the binding pocket, each formatted as chain_id,residue_idx on its own line. Each entry must have two parts and residue_idx must be an integer.</td><td style="word-wrap: break-word;">A,120 A,121 B,45</td></tr>
<tr><td style="word-wrap: break-word;">contact_token1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For contact constraints: semicolon-separated list of first residues in each contact pair, each formatted as chain_id,residue_idx. The total number of entries must match contact_token2, and each residue index must be an integer.</td><td style="word-wrap: break-word;">A,10;A,25;B,5</td></tr>
<tr><td style="word-wrap: break-word;">contact_token2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For contact constraints: semicolon-separated list of second residues in each contact pair, each formatted as chain_id,residue_idx. Must have the same number of entries as contact_token1, with valid integer residue indices.</td><td style="word-wrap: break-word;">B,30;B,40;C,15</td></tr>
<tr><td style="word-wrap: break-word;">max_distance</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Optional maximum distance in Ångströms for pocket and contact constraints. Values greater than 0 add a 'max_distance' field to each generated constraint of the selected type; 0 leaves it unset (no explicit limit at the YAML level). Valid range is 0.0 to 50.0.</td><td style="word-wrap: break-word;">8.0</td></tr>
<tr><td style="word-wrap: break-word;">force</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When true, adds a 'force': true flag to generated pocket or contact constraints, instructing Boltz to use potentials to actively enforce the constraint. Has no effect on bond constraints in this node.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">constraints</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list of constraint dictionaries formatted for the 'constraints' section of a Boltz YAML configuration. For constraint_type='bond', each element is of the form {"bond": {"atom1": [chain_id, residue_idx, atom_name], "atom2": [chain_id, residue_idx, atom_name]}}. For 'pocket', the list contains a single object like {"pocket": {"binder": binder_chain, "contacts": [[chain_id, residue_idx], ...], "max_distance"?: float, "force"?: true}}. For 'contact', each element is {"contact": {"token1": [chain_id, residue_idx], "token2": [chain_id, residue_idx], "max_distance"?: float, "force"?: true}}. This output is typically fed into Boltz List Combiner and then Boltz YAML Combiner.</td><td style="word-wrap: break-word;">[{"pocket": {"binder": "L", "contacts": [["A", 120], ["A", 121], ["B", 45]], "max_distance": 6.0, "force": true}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node performs simple string parsing and list construction, so it is fast even with many constraints; the main practical limit is human readability of very long input strings.
- **Limitations**: Only three constraint types are supported ('bond', 'pocket', 'contact'), and each relies on strict text formats with comma-separated fields and integer residue indices; it does not support arbitrary or custom Boltz constraint schemas.
- **Behavior**: For bond and contact types, the node requires that the parsed lists for the first and second partners have exactly the same length and contain at least one pair; otherwise it raises a descriptive error and produces no output.
- **Behavior**: max_distance and force are applied at the node level for pocket and contact constraints—max_distance is copied into every generated contact constraint (if >0) and force is applied uniformly when enabled; they cannot be varied per pair within a single node instance.
- **Behavior**: If, after parsing, no valid constraints are produced for the selected type (for example, empty inputs or all malformed lines), the node raises an error instead of silently outputting an empty list.

## Troubleshooting
- **Invalid bond_atom1 format: 'A,10'...**: The parser expected three comma-separated parts (chain_id,residue_idx,atom_name). Check each bond_atom1 and bond_atom2 entry includes all three parts (e.g., 'A,10,CA;B,25,N') and that residue_idx is an integer.
- **Number of bond_atom1 entries (N) must match number of bond_atom2 entries (M)**: The total number of semicolon-separated entries in bond_atom1 and bond_atom2 differ. Ensure they define the same number of atom pairs.
- **Invalid pocket_contacts format. Use: chain_id,residue_idx**: One or more lines in pocket_contacts does not have exactly two comma-separated parts or contains a non-integer residue index. Fix each line to the form 'A,120' and separate entries by newlines.
- **Both contact_token1 and contact_token2 are required for contact constraints**: constraint_type is 'contact' but at least one of the token fields is empty. Provide matching semicolon-separated lists of chain_id,residue_idx pairs in both fields.
- **No valid constraints found for type: bond/pocket/contact**: All relevant input fields for the selected constraint_type were empty or invalid, so the node could not construct any constraints. Review formatting, remove trailing semicolons or blank lines, and ensure at least one correctly formatted entry is provided.
