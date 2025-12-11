# Boltz Constraint Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates constraint objects for Boltz YAML configurations. Supports three constraint types—bond, pocket, and contact—and can generate multiple constraints at once using semicolon-separated inputs (or newline-separated for pocket contacts). Includes optional parameters such as maximum distance and a force flag to enforce constraints.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzconstraintnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to define structural or interaction constraints when preparing Boltz configuration data. Typical workflows: (1) pair protein atoms for covalent/geometry bonds, (2) specify a binder chain and its pocket contacts on a target, or (3) set residue-residue contact pairs across chains. Connect the resulting constraints output into subsequent Boltz list/YAML builder nodes.

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
<tr><td style="word-wrap: break-word;">constraint_type</td><td>True</td><td style="word-wrap: break-word;">COMBO</td><td style="word-wrap: break-word;">Select the type of constraint to build. Options: bond, pocket, contact.</td><td style="word-wrap: break-word;">pocket</td></tr>
<tr><td style="word-wrap: break-word;">bond_atom1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For bond type: semicolon-separated list of first atoms. Each entry: chain_id,residue_idx,atom_name.</td><td style="word-wrap: break-word;">A,1,N;B,2,CA</td></tr>
<tr><td style="word-wrap: break-word;">bond_atom2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For bond type: semicolon-separated list of second atoms corresponding positionally to bond_atom1. Each entry: chain_id,residue_idx,atom_name.</td><td style="word-wrap: break-word;">B,1,C;C,2,CB</td></tr>
<tr><td style="word-wrap: break-word;">pocket_binder</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For pocket type: chain ID of the binder (e.g., a ligand chain).</td><td style="word-wrap: break-word;">L</td></tr>
<tr><td style="word-wrap: break-word;">pocket_contacts</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For pocket type: newline-separated list of contact residues on the target. Each line: chain_id,residue_idx.</td><td style="word-wrap: break-word;">A,10 A,25 B,7</td></tr>
<tr><td style="word-wrap: break-word;">contact_token1</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For contact type: semicolon-separated list of first residues. Each entry: chain_id,residue_idx.</td><td style="word-wrap: break-word;">A,10;A,20;B,30</td></tr>
<tr><td style="word-wrap: break-word;">contact_token2</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">For contact type: semicolon-separated list of second residues corresponding positionally to contact_token1. Each entry: chain_id,residue_idx.</td><td style="word-wrap: break-word;">B,20;C,30;D,40</td></tr>
<tr><td style="word-wrap: break-word;">max_distance</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Optional maximum distance (Å). Applies to pocket and contact constraints when > 0. Set 0 for no limit.</td><td style="word-wrap: break-word;">6.0</td></tr>
<tr><td style="word-wrap: break-word;">force</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, adds a 'force' flag to the constraint to request enforcement via a potential.</td><td style="word-wrap: break-word;">true</td></tr>
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
<tr><td style="word-wrap: break-word;">constraints</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list of constraint objects ready to be combined into Boltz YAML. For bond/contact, one entry per input pair; for pocket, one entry containing the binder and its contacts.</td><td style="word-wrap: break-word;">[{"pocket": {"binder": "L", "contacts": [["A", 10], ["A", 25]] , "max_distance": 6.0, "force": true}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- For bond constraints, bond_atom1 and bond_atom2 are both required, must have the same number of entries, and each entry must be in the format chain_id,residue_idx,atom_name.
- For contact constraints, contact_token1 and contact_token2 are both required, must have the same number of entries, and each entry must be in the format chain_id,residue_idx.
- For pocket constraints, pocket_binder and pocket_contacts are both required. pocket_contacts must be newline-separated entries in the format chain_id,residue_idx.
- max_distance only appears in the output when greater than 0; otherwise it is omitted (no limit).
- Setting force to true adds a 'force' flag within the corresponding constraint.
- Empty or malformed inputs will raise validation errors; ensure counts and formats match exactly.

## Troubleshooting
- Invalid format error: Check each entry matches the required format. Bond entries need three comma-separated parts; contact/pocket entries need two.
- Mismatched counts (bond/contact): Ensure the number of entries in the first list equals the number of entries in the second list.
- No valid constraints found: Verify the selected constraint_type has all required fields filled with correctly formatted values.
- Pocket contacts not parsed: Ensure contacts are newline-separated, not semicolon-separated, and each line is chain_id,residue_idx.
- max_distance ignored: It is only included when greater than 0. Set a positive value if you need a threshold.
