# Boltz Constraint Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds one or more structural constraints in the Boltz YAML format. Supports bond, pocket, and contact constraints, with validation of formats and automatic batching when multiple pairs are provided. For bonds and contacts, multiple pairs can be specified using semicolons; pocket contacts are entered one per line.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzconstraintnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when defining geometric constraints to guide Boltz structure modeling. Select the constraint type and provide the appropriate tokens/atoms in the required format. Chain these constraints with sequence/template/property builders, then feed the combined configuration into downstream Boltz prediction nodes.

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
<tr><td style="word-wrap: break-word;">constraint_type</td><td>True</td><td style="word-wrap: break-word;">ENUM</td><td style="word-wrap: break-word;">Type of constraint to create. Determines which additional fields are used.</td><td style="word-wrap: break-word;">pocket</td></tr>
<tr><td style="word-wrap: break-word;">bond_atom1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">First atoms for bond constraints; semicolon-separated entries. Each entry must be chain_id,residue_idx,atom_name.</td><td style="word-wrap: break-word;">A,1,N;B,2,CA</td></tr>
<tr><td style="word-wrap: break-word;">bond_atom2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Second atoms for bond constraints; semicolon-separated entries. Must have the same number of entries as bond_atom1.</td><td style="word-wrap: break-word;">B,1,C;C,2,CB</td></tr>
<tr><td style="word-wrap: break-word;">pocket_binder</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Binder chain ID for pocket constraints.</td><td style="word-wrap: break-word;">L</td></tr>
<tr><td style="word-wrap: break-word;">pocket_contacts</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue contact list for pocket constraints; one per line. Each line must be chain_id,residue_idx.</td><td style="word-wrap: break-word;">A,10 A,12 B,30</td></tr>
<tr><td style="word-wrap: break-word;">contact_token1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">First tokens for contact constraints; semicolon-separated entries. Each entry must be chain_id,residue_idx.</td><td style="word-wrap: break-word;">A,10;A,20;B,30</td></tr>
<tr><td style="word-wrap: break-word;">contact_token2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Second tokens for contact constraints; semicolon-separated entries. Must have the same number of entries as contact_token1.</td><td style="word-wrap: break-word;">B,20;C,30;D,40</td></tr>
<tr><td style="word-wrap: break-word;">max_distance</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Optional maximum distance threshold in Angstroms for pocket or contact constraints. Set to 0 for no limit.</td><td style="word-wrap: break-word;">6.0</td></tr>
<tr><td style="word-wrap: break-word;">force</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to enforce the constraint with a potential.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">constraints</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list of constraint objects in Boltz YAML format, one per specified pair or pocket definition.</td><td style="word-wrap: break-word;">[{"bond": {"atom1": ["A", 1, "N"], "atom2": ["B", 2, "CA"]}}, {"contact": {"token1": ["A", 10], "token2": ["B", 20], "max_distance": 6.0, "force": true}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Formats are strict**: bond atoms require chain_id,residue_idx,atom_name; contact tokens require chain_id,residue_idx; pocket contacts require chain_id,residue_idx, one per line.
- **Multiple entries**: For bond and contact constraints, separate multiple pairs using semicolons in both corresponding fields, and ensure counts match.
- **Pocket entries**: pocket_contacts must be provided one per line; pocket_binder is a single chain ID.
- **Integer indices**: residue_idx must be integers; invalid numbers will raise errors.
- **Optional fields**: max_distance (0 means no limit) and force are only applied to pocket/contact constraints; bonds have no distance parameter.
- **Validation**: The node raises errors for missing required fields, mismatched counts, or invalid formats.

## Troubleshooting
- **Error: Both bond_atom1 and bond_atom2 are required for bond constraints**: Provide both fields with semicolon-separated lists of equal length.
- **Error: Number of ... entries must match**: Ensure the number of entries in bond_atom1 equals bond_atom2, or contact_token1 equals contact_token2.
- **Error: Invalid ... format**: Check each entry matches the required format. Examples: bond 'A,1,N'; contact 'A,10'; pocket 'A,10' (one per line).
- **Error: Binder and contacts are required for pocket constraints**: Set pocket_binder and at least one valid line in pocket_contacts.
- **No valid constraints found for type**: Verify that inputs are not empty and correctly formatted for the selected constraint_type.
- **Non-integer residue index**: Ensure residue indices are integers without spaces (e.g., 'A,10' not 'A,10.0' or 'A,ten').
