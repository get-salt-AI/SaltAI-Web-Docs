# Boltz Template Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a template object for Boltz YAML, wrapping a PDB structure and optional chain mapping. You can specify which target chain(s) to template, optionally map them to template chain IDs, and control enforcement with a distance threshold and a force flag.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltztemplatenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to guide structure prediction with a known PDB template. Connect a PDB structure source to structure_content, optionally set chain_ids (and matching template_ids) to map template chains to target chains, then combine its output with sequences/constraints/properties using a Boltz list combiner and a Boltz YAML combiner for prediction.

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
<tr><td style="word-wrap: break-word;">structure_content</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">PDB structure content to use as a template. Expects a file-like mapping where the first entry's content is the PDB text.</td><td style="word-wrap: break-word;">{'my_template.pdb': 'HEADER ...\\nATOM ...\\nEND'}</td></tr>
<tr><td style="word-wrap: break-word;">chain_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Target chain IDs to apply the template to. Comma-separated list (e.g., A,B). If one ID is provided, a single chain is targeted; multiple IDs target multiple chains.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">template_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Template chain IDs corresponding to chain_ids, comma-separated. Must be the same count as chain_ids to be applied. Maps which chain(s) from the PDB serve as the template for each target chain.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">force</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, adds a potential to enforce adherence to the template during modeling.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">threshold</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Distance threshold in Angstroms. Values > 0 limit deviations from the template to within this threshold; 0 means no limit.</td><td style="word-wrap: break-word;">2.0</td></tr>
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
<tr><td style="word-wrap: break-word;">templates</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one template object ready to be merged into a Boltz YAML configuration.</td><td style="word-wrap: break-word;">[{'pdb': 'ATOM ...', 'chain_id': ['A', 'B'], 'template_id': ['A', 'B'], 'force': True, 'threshold': 2.0}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Structure content format**: The input must be a mapping of filename to PDB text; the first entry's content is used.
- **Chain and template mapping**: chain_ids sets the target chain(s). template_ids is only applied if it contains the same number of entries as chain_ids.
- **Single vs multiple chains**: A single chain ID becomes a scalar in the output; multiple become a list.
- **Threshold semantics**: A threshold of 0 disables distance limiting; any positive value adds a 'threshold' field.
- **Force behavior**: Enabling force adds a 'force' field to strengthen adherence to the template during modeling.
- **No chain mapping**: If chain_ids is left empty, the template is included without explicit chain mapping.

## Troubleshooting
- **Error: 'Structure content is required'**: Ensure structure_content is connected and is a mapping with at least one PDB entry.
- **Error: 'Structure file content cannot be empty'**: The PDB text in structure_content must not be blank.
- **Template mapping not applied**: If template_ids count doesn't match chain_ids, the mapping is skipped. Provide equal-length, comma-separated lists.
- **Unexpected behavior with spaces**: Trim spaces around chain IDs and template IDs; use plain comma-separated values like 'A,B'.
- **No effect from threshold**: A threshold of 0 disables limiting; set a positive value to apply a distance threshold.
