# Boltz Template Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds a template object for Boltz YAML from a provided PDB structure. It extracts the first structure from the input, embeds its PDB text, and optionally sets chain and template identifiers, a force flag, and a distance threshold.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltztemplatenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to supply a structural template to a Boltz YAML configuration. Typically, connect a PDB structure source into this node, optionally specify chain IDs and matching template IDs, then feed the resulting template list into a list combiner and finally into a Boltz YAML combiner before prediction.

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
<tr><td style="word-wrap: break-word;">structure_content</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">Dictionary-like structure mapping names to PDB file contents. The node will use the first entry's PDB text as the template.</td><td style="word-wrap: break-word;">{'example_structure.pdb': 'ATOM ... END'}</td></tr>
<tr><td style="word-wrap: break-word;">chain_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated chain IDs to include in the template (e.g., A,B). A single value becomes a single string; multiple values become a list.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">template_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated template chain IDs corresponding to the provided chain_ids. Must match the number of chain_ids. A single value becomes a single string; multiple values become a list. Ignored if chain_ids is empty.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">force</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, adds a flag to enforce the template via a potential.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">threshold</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Distance threshold in Angstroms. Values > 0 are applied; 0 means no limit.</td><td style="word-wrap: break-word;">3.5</td></tr>
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
<tr><td style="word-wrap: break-word;">templates</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing a single template object for Boltz YAML. Includes the PDB text and any optional chain_id(s), template_id(s), force, and threshold fields.</td><td style="word-wrap: break-word;">[{'pdb': 'ATOM ... END', 'chain_id': ['A', 'B'], 'template_id': ['A', 'B'], 'force': True, 'threshold': 3.5}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **First-entry selection**: Only the first item in structure_content is used; ensure the desired PDB is first.
- **Non-empty PDB required**: The PDB text must be non-empty or the node will raise an error.
- **Chain/template ID pairing**: template_ids are only considered when chain_ids are provided, and the counts must match; otherwise template_id is not set and a warning is logged.
- **Single vs list behavior**: One chain_id/template_id becomes a single string; multiple become a list.
- **Threshold behavior**: A threshold of 0 disables distance limiting; only values > 0 are included.
- **Output shape**: The node returns a list with one template object, suitable for combining with other BOLTZ objects.

## Troubleshooting
- **Error: 'Structure content is required'**: Ensure structure_content is provided and is a dictionary-like mapping of names to PDB text.
- **Error: 'Structure file content cannot be empty'**: Provide valid, non-empty PDB content for the first entry.
- **Template IDs ignored**: If chain_ids is empty or the number of template_ids doesn't match chain_ids, template_id will not be set. Provide matching, comma-separated lists.
- **Unexpected output type**: The output is a list containing one template object. If a downstream node expects a list, connect directly; if it expects a single object, extract the first element via a list combiner or appropriate node.
- **Threshold not applied**: Threshold values of 0 are treated as 'no limit.' Set a value > 0 (within the allowed range) to include it.
