# Boltz Template Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates a template object for Boltz YAML configurations from a provided PDB structure. It extracts the first structure from the input batch and packages it with optional chain mapping, template ID mapping, enforcement flag, and distance threshold. Useful for guiding or constraining Boltz predictions with known structural information.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltztemplatenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to provide a structural template to a Boltz configuration. Connect a PDB dictionary (e.g., from a loader or combiner) and optionally specify which chains to use and how they should be mapped to template IDs. Enable 'force' to enforce the template via potentials, and set a 'threshold' to limit template application to residues within a distance cutoff.

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
<tr><td style="word-wrap: break-word;">structure_content</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary of PDB structures where keys are IDs and values are PDB file contents. Only the first entry will be used to build the template.</td><td style="word-wrap: break-word;">{'template_1': '<pdb-file-content>'}</td></tr>
<tr><td style="word-wrap: break-word;">chain_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated chain IDs from the input structure to include in the template (e.g., A,B). If one ID is provided, it is stored as a single value; if multiple, stored as a list.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">template_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated chain IDs to assign as template IDs. Must match the count of 'chain_ids' when provided; otherwise a warning is logged and they are ignored.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">force</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, adds a flag to enforce the template via potentials.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">threshold</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Distance threshold in Angstroms. If greater than 0, it will be included in the template to limit enforcement within this cutoff; 0 means no limit.</td><td style="word-wrap: break-word;">5.0</td></tr>
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
<tr><td style="word-wrap: break-word;">templates</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one Boltz template object built from the provided PDB and options.</td><td style="word-wrap: break-word;">[{'pdb': '<pdb-file-content>', 'chain_id': ['A', 'B'], 'template_id': ['A', 'B'], 'force': True, 'threshold': 5.0}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Only the first PDB entry in 'structure_content' is used; additional entries are ignored.
- If 'chain_ids' has a single value, 'chain_id' is saved as a string; for multiple values, it becomes a list.
- If 'template_ids' are provided, their count must match 'chain_ids' or they will be ignored and a warning will be logged.
- 'threshold' is only included if greater than 0.
- An error is raised if the PDB content is missing or empty.

## Troubleshooting
- Error: 'Structure content is required' — Ensure 'structure_content' is a non-empty dictionary of {id: pdb_string}.
- Error: 'Structure file content cannot be empty' — Verify the first PDB's content is a non-empty string.
- Unexpected template mapping — Check that 'template_ids' count matches 'chain_ids'; otherwise mappings are ignored.
- Template not constrained as expected — Set 'force' to true to enforce the template and consider setting a positive 'threshold' to control the enforcement region.
