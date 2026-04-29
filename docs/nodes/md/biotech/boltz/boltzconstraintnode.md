# Boltz Constraint Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

This node constructs constraint definitions in the Boltz YAML format for Boltz protein/ligand design and structure prediction workflows. It supports three constraint types—bond, pocket, and contact—and parses user-friendly text fields into the structured lists Boltz expects. Multiple constraints of the same type can be specified in one node using semicolon- or newline-separated entries.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzconstraintnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to encode structural or interaction constraints into a Boltz YAML configuration (for example, forcing a bond between atoms, defining a ligand-binding pocket, or constraining residue–residue contacts). It is typically placed after you define sequences with nodes like "Boltz Sequence Builder", "Boltz Protein Sequence", or "Boltz Ligand Sequence", and before combining everything with "Boltz List Combiner" and "Boltz YAML Combiner" followed by "Boltz Predict" for inference. For bond constraints, you pair specific atoms across residues; for pocket constraints, you define a binder chain and a set of receptor residues that form a binding pocket; for contact constraints, you specify pairs of residues that should remain within a distance threshold. Best practice is to start with a small set of well-understood constraints, validate formats carefully (chain IDs and residue indices must match your sequences), and then iteratively add more constraints as needed.

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
<tr><td style="word-wrap: break-word;">constraint_type</td><td>True</td><td style="word-wrap: break-word;">STRING (enum: bond, pocket, contact)</td><td style="word-wrap: break-word;">Selects the type of constraint to build. "bond" creates atom–atom bonds; "pocket" defines a binder chain and a set of receptor contact residues; "contact" defines residue–residue distance constraints between tokens. Determines which other fields are required and how they are interpreted.</td><td style="word-wrap: break-word;">pocket</td></tr>
<tr><td style="word-wrap: break-word;">bond_atom1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For constraint_type = "bond". Semicolon-separated list of first atoms in each bond pair, each as chain_id,residue_idx,atom_name. Residue index must be an integer; atom name is a valid atom identifier in the residue. The number of entries must match bond_atom2. Leave empty for non-bond constraints.</td><td style="word-wrap: break-word;">A,25,SG;B,10,N</td></tr>
<tr><td style="word-wrap: break-word;">bond_atom2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For constraint_type = "bond". Semicolon-separated list of second atoms in each bond pair, each as chain_id,residue_idx,atom_name. Must be the same count as bond_atom1 and in corresponding order. Used together with bond_atom1 to define multiple bond constraints in one node.</td><td style="word-wrap: break-word;">A,80,SG;C,15,C</td></tr>
<tr><td style="word-wrap: break-word;">pocket_binder</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For constraint_type = "pocket". The chain ID of the binder (e.g., ligand or peptide chain) defined in your sequences. Required for pocket constraints, ignored for others. Must match a chain id from your Boltz sequences.</td><td style="word-wrap: break-word;">L</td></tr>
<tr><td style="word-wrap: break-word;">pocket_contacts</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For constraint_type = "pocket". Newline-separated list of receptor contact residues, each as chain_id,residue_idx (no atom name). For example, these might be residues forming a pocket on the receptor surface. Parsed line by line; each line must have exactly two comma-separated parts.</td><td style="word-wrap: break-word;">A,45\nA,46\nA,101\nB,30</td></tr>
<tr><td style="word-wrap: break-word;">contact_token1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For constraint_type = "contact". Semicolon-separated list of first residues (tokens) in each contact pair, each as chain_id,residue_idx. The number of entries must match contact_token2; each pair defines one contact constraint.</td><td style="word-wrap: break-word;">A,120;A,121;B,15</td></tr>
<tr><td style="word-wrap: break-word;">contact_token2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For constraint_type = "contact". Semicolon-separated list of second residues in each contact pair, each as chain_id,residue_idx. Must be equal in length and aligned with contact_token1 entries. Each pair constrains the distance between two residues.</td><td style="word-wrap: break-word;">B,35;B,36;C,5</td></tr>
<tr><td style="word-wrap: break-word;">max_distance</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Maximum allowed distance in Ångströms for pocket and contact constraints. A value of 0.0 means no explicit distance limit is added to the constraint. For pocket constraints it is stored as pocket.max_distance, and for contact constraints as contact.max_distance for each pair.</td><td style="word-wrap: break-word;">8.0</td></tr>
<tr><td style="word-wrap: break-word;">force</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">When true, instructs Boltz to use an explicit potential to enforce the constraint more strongly. For pocket constraints it sets pocket.force to true, and for contact constraints it sets contact.force to true on each generated pair. Has no effect for bond constraints.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">constraints</td><td style="word-wrap: break-word;">* (list of dicts)</td><td style="word-wrap: break-word;">A list of Boltz constraint objects ready to be merged into a Boltz YAML configuration. Each list element is a dictionary with a single top-level key: "bond", "pocket", or "contact", containing the structured fields derived from the inputs. This list is designed to be passed to Boltz List Combiner and then into Boltz YAML Combiner as its constraints argument.</td><td style="word-wrap: break-word;">[{"pocket": {"binder": "L", "contacts": [["A", 45], ["A", 46], ["A", 101]], "max_distance": 8.0, "force": true}}, {"contact": {"token1": ["A", 120], "token2": ["B", 35], "max_distance": 6.0, "force": true}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Performance**: The node itself is lightweight; most cost is in Boltz inference later. However, large numbers of constraints can slow downstream Boltz runs and make convergence harder.
- **Limitations**: bond constraints only support atom-level specification (chain_id,residue_idx,atom_name) and require one-to-one pairing; you cannot currently specify ranges or patterns directly.
- **Behavior**: For bond and contact types, the parser expects semicolon-separated entries and will fail fast with a clear error if counts do not match or residue indices are non-integer.
- **Behavior**: A max_distance of 0.0 means no distance field is added to the constraint object; it does not mean zero distance, but rather no explicit upper bound.
- **Limitations**: The node does not verify that chain IDs or residue indices actually exist in your sequences; mismatches will only surface later during Boltz execution.

## Troubleshooting
- **Invalid bond_atom1 format**: Error like "Invalid bond_atom1 format: 'A,10'. Expected: chain_id,residue_idx,atom_name" indicates that entries for bond pairs must include three comma-separated parts, such as "A,10,CA".
- **Mismatched entry counts**: Errors such as "Number of bond_atom1 entries (2) must match number of bond_atom2 entries (1)" or the equivalent for contact tokens mean you provided a different number of pairs on each side; ensure both fields have the same number of semicolon-separated entries.
- **Pocket contacts format error**: A message "Invalid contact format. Use: chain_id,residue_idx" or "Invalid pocket_contacts format: 'A10'. Expected: chain_id,residue_idx" means your newline-separated pocket_contacts are missing a comma or the residue index is not an integer. Use lines like "A,45".
- **No valid constraints found**: If you see "No valid constraints found for type: pocket" (or another type), the inputs for that type were empty or fully invalid. Confirm that the relevant fields (for example, pocket_binder and pocket_contacts for pocket) are filled and correctly formatted for the chosen constraint_type.
