# Boltz Constraint Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds constraint specification objects for Boltz YAML configurations. Supports creating bond, pocket, and contact constraints, with flexible multi-entry text fields (semicolon or newline separated) and optional distance/force parameters. Produces a structured constraints payload ready to be combined with other Boltz components.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzconstraintnode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you need to impose geometric or interaction constraints on a Boltz prediction or partial diffusion run. A typical workflow is: define one or more constraints with this node, collect them (and any other Boltz objects) using Boltz List Combiner, then assemble a complete configuration with Boltz YAML Combiner before sending it to a Boltz execution node.

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
<tr><td style="word-wrap: break-word;">constraint_type</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Type of constraint to build, such as bond, pocket, or contact. This choice controls which other fields are interpreted and how they are mapped into the final constraint object.</td><td style="word-wrap: break-word;">bond</td></tr>
<tr><td style="word-wrap: break-word;">chain_ids</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chain ID or IDs involved in the constraint. When multiple chains are needed (for example, cross-chain contacts), provide them as semicolon- or newline-separated values.</td><td style="word-wrap: break-word;">A;B</td></tr>
<tr><td style="word-wrap: break-word;">residue_indices</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Residue positions referenced by the constraint. Supports multiple residues or residue pairs depending on constraint_type, entered as semicolon- or newline-separated indices.</td><td style="word-wrap: break-word;">45;60;102</td></tr>
<tr><td style="word-wrap: break-word;">target_distance</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Target distance (for example, in Å) between constrained residues or atoms for bond/contact-style constraints.</td><td style="word-wrap: break-word;">3.5</td></tr>
<tr><td style="word-wrap: break-word;">tolerance</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Allowed deviation from the target_distance. Controls how tightly the distance is enforced.</td><td style="word-wrap: break-word;">0.5</td></tr>
<tr><td style="word-wrap: break-word;">force_constant</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Relative strength or weight of this constraint in the Boltz run. Higher values make the constraint more influential.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">pocket_center</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Definition of the pocket center for pocket-type constraints, typically as one or more chain:residue specifications. Only used when constraint_type is pocket-like.</td><td style="word-wrap: break-word;">A:45;A:47;A:50</td></tr>
<tr><td style="word-wrap: break-word;">radius</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Radius or spatial extent of the pocket or region in pocket-style constraints.</td><td style="word-wrap: break-word;">6.0</td></tr>
<tr><td style="word-wrap: break-word;">label</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional human-readable name or tag for this constraint, useful for keeping track of many constraints in complex setups.</td><td style="word-wrap: break-word;">Active-site restraint</td></tr>
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
<tr><td style="word-wrap: break-word;">constraints</td><td style="word-wrap: break-word;">BOLTZ_CONSTRAINT</td><td style="word-wrap: break-word;">A Boltz-compatible constraint object (or list of constraints) encoding the specified bond, pocket, or contact parameters, ready to be merged into a Boltz YAML configuration.</td><td style="word-wrap: break-word;">Not specified</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Multi-entry fields**: Fields like chain_ids and residue_indices accept multiple values separated by semicolons or newlines. Inconsistent separators or stray characters can cause malformed constraints.
- **Type-specific relevance**: Some fields only matter for certain constraint types (for example, pocket_center and radius for pocket constraints, target_distance and force_constant for bond or contact constraints); unused fields are ignored.
- **Requires downstream combination**: This node does not execute Boltz. Its output must be passed through Boltz List Combiner and Boltz YAML Combiner, together with sequence/template/property objects, to influence a Boltz run.
- **Identifier consistency**: Chain IDs and residue indices must match the chains and residues defined in your sequence and template configuration; otherwise, constraints may be silently ineffective.

## Troubleshooting
- **Constraints have no visible effect**: Confirm that this node’s output is connected into Boltz List Combiner and Boltz YAML Combiner and that the resulting YAML is actually used by your execution node. Also verify that chain_ids and residue_indices match your model.
- **Input validation errors**: If the node fails, check that all required fields for the chosen constraint_type are filled, and that multi-value text fields contain only valid chain IDs and integer residue indices separated by semicolons or newlines.
- **Structures look over-constrained or distorted**: Consider relaxing target_distance, increasing tolerance, or lowering force_constant. Overly strict or strong constraints can force unrealistic geometries.
- **Pocket constraint seems ignored**: Make sure constraint_type is set to a pocket-capable mode and that pocket_center and radius are filled with valid chain:residue references and a reasonable radius value.
