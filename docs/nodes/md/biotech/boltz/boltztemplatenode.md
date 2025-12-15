# Boltz Template Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a Boltz-compliant template object from a provided PDB structure. You can limit templating to specific chains, map them to template IDs, and optionally enforce the template with a potential and/or apply a distance threshold.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltztemplatenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when preparing template specifications for Boltz YAML workflows. Feed it PDB structure content, optionally restrict to certain chain IDs, map to specific template IDs, and set enforcement or threshold parameters before combining with sequences, constraints, and properties for prediction or design.

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
<tr><td style="word-wrap: break-word;">structure_content</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">PDB structure content to use for templating. The node consumes the first entry if multiple are present.</td><td style="word-wrap: break-word;">{'protein.pdb': 'ATOM ... (PDB text) ... END'}</td></tr>
<tr><td style="word-wrap: break-word;">chain_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated chain IDs from the structure to use in the template. If one chain is provided, it is stored as a single value; multiple chains are stored as a list. Leave empty to use all chains.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">template_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated template chain IDs corresponding to chain_ids. Must match the count of chain_ids; otherwise, they are ignored.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">force</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, includes a flag to enforce the template via a potential.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">threshold</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Distance threshold in Angstroms; 0 means no limit. Values > 0 are added to the template.</td><td style="word-wrap: break-word;">2.0</td></tr>
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
<tr><td style="word-wrap: break-word;">templates</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one Boltz template object assembled from the inputs.</td><td style="word-wrap: break-word;">[{'pdb': '<PDB text>', 'chain_id': 'A', 'template_id': 'A', 'force': True, 'threshold': 2.0}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- If structure_content contains multiple entries, only the first is used.
- Leave chain_ids empty to template the whole structure; providing one chain produces a single value, multiple chains produce a list.
- template_ids must match the number of chain_ids; otherwise they are ignored and a warning is logged.
- threshold of 0 disables the distance limit; only positive values are included in the output.
- force adds an enforcement flag to the template object but does not modify geometry.

## Troubleshooting
- Error: "Structure content is required" — Ensure structure_content is provided and is a PDB-type object with at least one entry.
- Error: "Structure file content cannot be empty" — Confirm the PDB text is non-empty.
- Template IDs not applied — Check that template_ids count exactly matches chain_ids count.
- Unexpected chains in output — Verify chain_ids is set correctly (e.g., "A,B" with no spaces beyond commas).
- Threshold not present in output — Only values > 0 are included; set a positive number within the allowed range.
