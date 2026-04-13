# Boltz Template Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a Boltz-compliant template object from a provided PDB structure. You can restrict templating to specific chains, remap them to template IDs, and optionally enforce the template with a potential and/or apply a distance threshold. The output is a structured template specification ready to be combined into a Boltz YAML configuration.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltztemplatenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to incorporate structural templates into a Boltz run for prediction or design. Provide PDB content for a structure you want to use as a template, optionally specify which chains to use and how they map to template IDs, and set enforcement strength or distance thresholds if you want the model to follow the template more strictly. The resulting template object is typically combined with sequences, constraints, and properties via Boltz List Combiner and Boltz YAML Combiner before sending to downstream Boltz execution nodes.

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
<tr><td style="word-wrap: break-word;">pdb_content</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">The full text content of the PDB file that defines the structural template. This structure is parsed and converted into a Boltz template specification.</td><td style="word-wrap: break-word;">ATOM      1  N   MET A   1      11.104  13.207   8.678  1.00 20.00           N  ...</td></tr>
<tr><td style="word-wrap: break-word;">chain_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional list of chain IDs from the PDB to use as templates. If left empty, all chains in the PDB may be considered. Multiple chains are typically separated by commas, semicolons, or newlines depending on interface conventions.</td><td style="word-wrap: break-word;">A;B</td></tr>
<tr><td style="word-wrap: break-word;">template_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional mapping from PDB chain IDs to template IDs used in the Boltz YAML. If provided, it should have the same number of entries as chain_ids so each chain can be mapped to a desired template identifier.</td><td style="word-wrap: break-word;">chainA_template;chainB_template</td></tr>
<tr><td style="word-wrap: break-word;">enforce_template</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">Whether to enforce the provided template structure during Boltz runs by applying a template potential. When enabled, the model is encouraged to remain close to the template geometry.</td><td style="word-wrap: break-word;">true</td></tr>
<tr><td style="word-wrap: break-word;">template_potential_weight</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Strength or weight of the template enforcement potential, if enforcement is enabled. Higher values more strongly penalize deviations from the template structure.</td><td style="word-wrap: break-word;">1.0</td></tr>
<tr><td style="word-wrap: break-word;">distance_threshold</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Optional distance cutoff (in angstroms) for template application. Atoms or residues beyond this threshold may be ignored or down-weighted in the template specification.</td><td style="word-wrap: break-word;">8.0</td></tr>
<tr><td style="word-wrap: break-word;">name</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Optional human-readable name or label for this template, stored in the resulting Boltz object for easier identification when combined with other components.</td><td style="word-wrap: break-word;">RBD_template_v1</td></tr>
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
<tr><td style="word-wrap: break-word;">boltz_template</td><td style="word-wrap: break-word;">BOLTZ_TEMPLATE</td><td style="word-wrap: break-word;">A Boltz-compatible template specification object derived from the input PDB and options. This object can be combined with other Boltz components such as sequences, constraints, and properties to form a complete YAML configuration.</td><td style="word-wrap: break-word;">{"type":"template","chains":[{"pdb_chain_id":"A","template_id":"chainA_template"}],"enforce":true,"weight":1.0,"distance_threshold":8.0}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Template source quality**: The node assumes the PDB content is valid and well-formed; malformed PDBs can lead to parsing failures or invalid templates.
- **Chain and template mapping**: If you specify chain_ids, ensure they exist in the PDB and that template_ids, when provided, align one-to-one with them; inconsistent lists can cause validation errors.
- **Enforcement strength**: Very large template_potential_weight values may over-constrain the model and reduce exploration; adjust gradually based on behavior.
- **Distance threshold semantics**: The distance_threshold is generally interpreted in angstroms and affects which parts of the template are considered; leaving it unset usually applies the template more broadly.
- **Workflow position**: This node only builds a template object; you must still combine it with other Boltz components using the relevant combiner nodes before running any prediction or design step.

## Troubleshooting
- **Issue: Node errors when given PDB content**: Verify that the PDB text is complete, not truncated, and uses standard PDB formatting, including proper ATOM or HETATM records and valid chain IDs.
- **Issue: Expected template chains are missing in the output**: Check that the chain_ids you specified actually exist in the PDB and that there are no extra spaces or case mismatches (for example, 'a' instead of 'A').
- **Issue: Mismatch or validation error for chain_ids and template_ids**: Ensure the number of entries in template_ids matches chain_ids and that they are separated using the same delimiter convention supported in the interface.
- **Issue: Model appears too constrained by the template**: Lower the template_potential_weight or disable enforce_template to allow more structural flexibility during sampling.
- **Issue: Template seems to have little effect**: Confirm enforce_template is enabled, modestly increase template_potential_weight, or reduce distance_threshold so that more of the structure is actively enforced.
