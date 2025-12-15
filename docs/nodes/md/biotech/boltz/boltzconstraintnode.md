# Boltz Constraint Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates constraint specifications for Boltz YAML. Supports bond, pocket, and contact constraints, with flexible multi-entry inputs using semicolon or newline separation and optional distance/force parameters.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzconstraintnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define geometric or interaction constraints for your Boltz run. Build one or more constraints, then combine them (optionally with sequences, templates, and properties) using Boltz List Combiner and Boltz YAML Combiner before running prediction or partial diffusion.

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
<tr><td style="word-wrap: break-word;">constraint_type</td><td>True</td><td style="word-wrap: break-word;">["bond", "pocket", "contact"]</td><td style="word-wrap: break-word;">Selects the type of constraint to build.</td><td style="word-wrap: break-word;">pocket</td></tr>
<tr><td style="word-wrap: break-word;">bond_atom1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For bond constraints: semicolon-separated list of atom tokens for the first atom in each pair. Each token is chain_id,residue_idx,atom_name.</td><td style="word-wrap: break-word;">A,1,N;B,2,CA</td></tr>
<tr><td style="word-wrap: break-word;">bond_atom2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For bond constraints: semicolon-separated list of atom tokens for the second atom in each pair. Must have the same number of entries as bond_atom1. Each token is chain_id,residue_idx,atom_name.</td><td style="word-wrap: break-word;">A,1,C;B,2,CB</td></tr>
<tr><td style="word-wrap: break-word;">pocket_binder</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For pocket constraints: the binder chain ID.</td><td style="word-wrap: break-word;">L</td></tr>
<tr><td style="word-wrap: break-word;">pocket_contacts</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For pocket constraints: newline-separated list of contact tokens. Each line is chain_id,residue_idx.</td><td style="word-wrap: break-word;">A,10 A,25 B,5</td></tr>
<tr><td style="word-wrap: break-word;">contact_token1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For contact constraints: semicolon-separated list of first residue tokens. Each token is chain_id,residue_idx.</td><td style="word-wrap: break-word;">A,10;B,20</td></tr>
<tr><td style="word-wrap: break-word;">contact_token2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For contact constraints: semicolon-separated list of second residue tokens. Must have the same number of entries as contact_token1. Each token is chain_id,residue_idx.</td><td style="word-wrap: break-word;">L,5;L,15</td></tr>
<tr><td style="word-wrap: break-word;">max_distance</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Optional maximum distance in Å for pocket/contact constraints. 0 means no explicit limit is set.</td><td style="word-wrap: break-word;">8.0</td></tr>
<tr><td style="word-wrap: break-word;">force</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, marks the constraint to be enforced using potentials.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">constraints</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list of constraint objects ready to be merged into a Boltz YAML configuration.</td><td style="word-wrap: break-word;">[{"pocket": {"binder": "L", "contacts": [["A", 10], ["A", 25], ["B", 5]], "max_distance": 8.0, "force": true}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Input formats**: bond_atom* use semicolons between entries and require chain_id,residue_idx,atom_name; contact_token* use semicolons and require chain_id,residue_idx; pocket_contacts uses newline-separated entries with chain_id,residue_idx per line.
- **Paired counts must match**: bond_atom1 and bond_atom2 must have the same number of entries; contact_token1 and contact_token2 must have the same number of entries.
- **Distance handling**: max_distance applies to pocket and contact constraints; if set to 0, the field is omitted (no limit).
- **Force flag**: Setting force to true adds a flag to enforce the constraint with potentials.
- **Multiple constraints**: You can specify multiple bond/contact pairs in one node using semicolon-separated lists.

## Troubleshooting
- **Mismatch in entry counts**: If you see an error like 'Number of ... entries must match', ensure both paired fields (e.g., bond_atom1 and bond_atom2) have the same number of semicolon-separated items.
- **Invalid format error**: Ensure each entry strictly follows the required format (e.g., A,10 or A,1,N). Residue indices must be integers; atom names are required for bond constraints.
- **Empty input error**: For a chosen constraint type, required fields cannot be empty (e.g., pocket requires pocket_binder and pocket_contacts).
- **Wrong separator**: Pocket contacts require newline separation, not semicolons. Bond/contact lists require semicolons between entries.
- **No constraints produced**: If no valid constraints are parsed, verify that inputs are non-empty and correctly formatted for the selected constraint type.
