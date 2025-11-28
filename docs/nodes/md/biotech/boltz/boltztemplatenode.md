# Boltz Template Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Builds template entries for Boltz YAML configurations from a provided PDB structure. Supports selecting specific chain IDs, mapping them to template IDs, optionally enforcing the template during modeling, and applying an interatomic distance threshold.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltztemplatenode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to provide a structural template (PDB) to guide Boltz modeling. Typical workflow: supply a PDB file, optionally specify which chains to use and how to map them to template IDs, then connect the output to a list-combiner and finally into a Boltz YAML combiner before prediction.

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
<tr><td style="word-wrap: break-word;">structure_content</td><td>True</td><td style="word-wrap: break-word;">PDB</td><td style="word-wrap: break-word;">PDB structure content to use as a template. Provide a file-like input containing the text of a PDB file.</td><td style="word-wrap: break-word;">{'example.pdb': 'ATOM  ... PDB CONTENT ...'}</td></tr>
<tr><td style="word-wrap: break-word;">chain_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated chain identifiers from the structure to use as templates (e.g., A,B). If left empty, chain selection is not constrained.</td><td style="word-wrap: break-word;">A,B</td></tr>
<tr><td style="word-wrap: break-word;">template_ids</td><td>False</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Comma-separated template chain IDs to pair 1:1 with chain_ids (e.g., A,B). The number of entries must match chain_ids when provided.</td><td style="word-wrap: break-word;">X,Y</td></tr>
<tr><td style="word-wrap: break-word;">force</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, adds a flag to enforce use of the template during modeling.</td><td style="word-wrap: break-word;">True</td></tr>
<tr><td style="word-wrap: break-word;">threshold</td><td>False</td><td style="word-wrap: break-word;">FLOAT</td><td style="word-wrap: break-word;">Distance threshold in Angstroms to constrain template application. 0 means no limit.</td><td style="word-wrap: break-word;">3.5</td></tr>
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
<tr><td style="word-wrap: break-word;">templates</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list containing one template object built from the PDB and options. This list can be combined with other items and fed into a Boltz YAML combiner.</td><td style="word-wrap: break-word;">[{'pdb': 'ATOM  ... PDB CONTENT ...', 'chain_id': ['A', 'B'], 'template_id': ['X', 'Y'], 'force': True, 'threshold': 3.5}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **Chain-ID mapping**: If chain_ids are provided, template_ids (if set) must contain the same number of entries to ensure correct 1:1 mapping.
- **Single vs multiple chains**: When a single chain ID is provided, it may be recorded as a single value; multiple chains are recorded as a list.
- **Threshold behavior**: A threshold of 0 applies no distance limit; any positive value will be included in the template.
- **Force flag**: Enabling force adds a directive to more strongly enforce the template during modeling.
- **Input validity**: The PDB content must be non-empty and valid text; empty or missing structure content will cause an error.

## Troubleshooting
- **Error: 'Structure content is required'**: Ensure you connected a valid PDB input and it contains text (not empty).
- **Error: 'Structure file content cannot be empty'**: The PDB file is present but empty. Provide a proper PDB file.
- **Warning: 'Number of template IDs must match number of chain IDs'**: Check that template_ids has the same number of items as chain_ids. Use comma-separated lists with matching counts.
- **Unexpected result: No chain filtering applied**: If chain_ids is left blank, the template won’t be limited to specific chains.
- **Downstream combine issues**: If the Boltz YAML combiner expects a list, connect this node’s output directly or via a list combiner without altering its list structure.
