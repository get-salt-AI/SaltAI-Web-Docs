# Boltz Constraint Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates constraint objects for Boltz YAML configurations. Supports three constraint types—bond, pocket, and contact—and allows batching multiple constraints in one node by providing semicolon-separated entries. Outputs a list of constraint mappings ready to be combined into a full Boltz YAML.

</div>
<div style="flex: 0 0 300px;"><img src="../../../images/previews/new-uncategorized/boltzconstraintnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define structural constraints for your Boltz runs. Choose a constraint type and provide the corresponding atom or residue specifications. For multiple constraints of the same type, provide matching semicolon-separated lists. Connect the resulting output to a list combiner or directly into a Boltz YAML combiner node alongside sequences and templates.

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
<tr><td style="word-wrap: break-word;">constraint_type</td><td>True</td><td style="word-wrap: break-word;">CHOICE</td><td style="word-wrap: break-word;">Type of constraint to generate. Select one of: bond, pocket, or contact.</td><td style="word-wrap: break-word;">pocket</td></tr>
<tr><td style="word-wrap: break-word;">bond_atom1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For bond constraints: semicolon-separated list of the first atoms. Format per entry: chain_id,residue_idx,atom_name.</td><td style="word-wrap: break-word;">A,15,N;A,25,CA</td></tr>
<tr><td style="word-wrap: break-word;">bond_atom2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For bond constraints: semicolon-separated list of the second atoms. Must have the same number of entries as bond_atom1. Format per entry: chain_id,residue_idx,atom_name.</td><td style="word-wrap: break-word;">B,40,C;B,50,CB</td></tr>
<tr><td style="word-wrap: break-word;">pocket_binder</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For pocket constraints: chain ID of the binder (typically the ligand chain).</td><td style="word-wrap: break-word;">L</td></tr>
<tr><td style="word-wrap: break-word;">pocket_contacts</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For pocket constraints: semicolon-separated list of residue contacts on the receptor. Format per entry: chain_id,residue_idx.</td><td style="word-wrap: break-word;">A,120;A,121;B,45</td></tr>
<tr><td style="word-wrap: break-word;">contact_token1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For contact constraints: semicolon-separated list of first residues. Format per entry: chain_id,residue_idx.</td><td style="word-wrap: break-word;">A,10;A,20;B,30</td></tr>
<tr><td style="word-wrap: break-word;">contact_token2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For contact constraints: semicolon-separated list of second residues. Must have the same number of entries as contact_token1. Format per entry: chain_id,residue_idx.</td><td style="word-wrap: break-word;">B,20;C,30;D,40</td></tr>
<tr><td style="word-wrap: break-word;">max_distance</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Optional maximum distance in Angstroms for pocket/contact constraints. A value of 0 means no distance limit is applied.</td><td style="word-wrap: break-word;">8.0</td></tr>
<tr><td style="word-wrap: break-word;">force</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, adds a potential to enforce the constraint during sampling.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">constraints</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list of constraint objects matching the selected type. Each object is structured for direct inclusion under the constraints section of a Boltz YAML.</td><td style="word-wrap: break-word;">[{'pocket': {'binder': 'L', 'contacts': [['A', 120], ['A', 121], ['B', 45]], 'max_distance': 8.0, 'force': True}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Semicolon-separated entries**: For multiple constraints in one field, separate entries with semicolons. Each entry must follow the exact format indicated.
- **Matching list lengths**: For bond and contact constraints, the two lists (atom1/atom2 or token1/token2) must have the same number of entries.
- **Distance handling**: max_distance only applies if greater than 0; 0 disables distance limiting.
- **Force flag**: Setting force to true adds a potential to enforce the constraint during inference; leave false for soft behavior.
- **Chain and residue IDs**: Ensure chain IDs and residue indices correspond to those used in your sequences/templates.
- **Formatting consistency**: Although the UI may show multiline fields, the parser expects semicolon-separated entries; do not rely on newlines alone.

## Troubleshooting
- **Invalid format errors**: Verify each entry matches the required pattern. For bonds use chain,residue,atom (e.g., A,15,CA). For contacts/pocket use chain,residue (e.g., A,120). Separate multiple entries with semicolons.
- **Mismatched entry counts**: If you get an error about list lengths, ensure bond_atom1 has the same number of entries as bond_atom2, and contact_token1 matches contact_token2.
- **No constraints found**: This occurs if required fields for the chosen type are empty. Provide the necessary inputs for the selected constraint_type.
- **Unknown chain or residue**: If results look incorrect, confirm that chain IDs and residue indices exist in the provided sequences/templates.
- **Unexpected distance behavior**: If constraints seem ineffective, check that max_distance is greater than 0; otherwise, no limit is applied.
