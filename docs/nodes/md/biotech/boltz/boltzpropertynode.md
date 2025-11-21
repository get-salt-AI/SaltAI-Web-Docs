# Boltz Property Builder

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Creates property specifications to include in a Boltz YAML configuration. Currently supports affinity prediction by specifying which chain is the binder (typically a ligand). Outputs a properties list ready to be merged with sequences, constraints, and templates.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/boltz/boltzpropertynode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you want to request additional properties (e.g., affinity) during structure prediction. Provide the binder chain ID (matching the ligand chain in your sequences), then combine the resulting properties with sequences (and optionally constraints/templates) using Boltz List Combiner and feed them into the Boltz YAML Combiner before running prediction.

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
<tr><td style="word-wrap: break-word;">property_type</td><td>True</td><td style="word-wrap: break-word;">enum['affinity']</td><td style="word-wrap: break-word;">Which property to compute. Only 'affinity' is supported.</td><td style="word-wrap: break-word;">affinity</td></tr>
<tr><td style="word-wrap: break-word;">binder_chain</td><td>True</td><td style="word-wrap: break-word;">STRING</td><td style="word-wrap: break-word;">Chain ID of the binder (usually a ligand) to evaluate affinity for. Must correspond to a ligand chain defined in the sequences.</td><td style="word-wrap: break-word;">L</td></tr>
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
<tr><td style="word-wrap: break-word;">properties</td><td style="word-wrap: break-word;">*</td><td style="word-wrap: break-word;">A list of property entries to be included under 'properties' in the Boltz YAML. For affinity, each entry specifies the binder chain.</td><td style="word-wrap: break-word;">[{'affinity': {'binder': 'L'}}]</td></tr>
</tbody>
</table>
</div>

## Important Notes
- Only 'affinity' property is supported by this node.
- Binder chain ID is required and must match a ligand chain defined in the sequences.
- To actually compute affinity, your YAML must include at least one ligand sequence; the downstream prediction will validate this.
- The output should be combined with other BOLTZ objects using Boltz List Combiner and then passed to the Boltz YAML Combiner.

## Troubleshooting
- Error: 'Binder chain ID is required' — Provide a non-empty binder_chain matching your ligand chain (e.g., 'L').
- Error: 'Unsupported property type' — The node currently supports only 'affinity'. Select 'affinity'.
- Downstream error: 'Affinity prediction requires at least one ligand sequence' — Ensure your sequences include a ligand entry with the same chain ID as 'binder_chain'.
