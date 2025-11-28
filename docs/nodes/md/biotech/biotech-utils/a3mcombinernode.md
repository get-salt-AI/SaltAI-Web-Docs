# A3M Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines up to five A3M MSA dictionaries into a single A3M output. It can optionally prefix chain IDs (e.g., A3M1_, A3M2_) to avoid naming conflicts and will ensure unique keys even if conflicts occur.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/a3mcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have multiple A3M MSAs (e.g., from different sources or segments) that need to be merged into one input for downstream modeling or analysis. Connect one or more A3M dictionary outputs to the available inputs, enable chain prefixing if there is any chance of overlapping chain IDs, and pass the resulting combined dictionary to subsequent biotech nodes.

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
<tr><td style="word-wrap: break-word;">a3m_1</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">First A3M MSA dictionary. Keys are chain IDs; values are A3M-formatted strings.</td><td style="word-wrap: break-word;">{'A': '<a3m-alignment-for-chain-A>', 'B': '<a3m-alignment-for-chain-B>'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_2</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Second A3M MSA dictionary to merge.</td><td style="word-wrap: break-word;">{'A': '<a3m-alignment>', 'C': '<a3m-alignment>'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_3</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Third A3M MSA dictionary to merge.</td><td style="word-wrap: break-word;">{'D': '<a3m-alignment>'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_4</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Fourth A3M MSA dictionary to merge.</td><td style="word-wrap: break-word;">{'E': '<a3m-alignment>'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_5</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Fifth A3M MSA dictionary to merge.</td><td style="word-wrap: break-word;">{'F': '<a3m-alignment>'}</td></tr>
<tr><td style="word-wrap: break-word;">prefix_chains</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, prefixes chain IDs with the source index (A3M1_, A3M2_, etc.) to avoid collisions.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_a3m</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">A single A3M dictionary containing all merged chains with unique IDs.</td><td style="word-wrap: break-word;">{'A3M1_A': '<a3m-alignment>', 'A3M2_A': '<a3m-alignment>', 'B': '<a3m-alignment>'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- At least one A3M input is required; otherwise the node raises an error.
- If prefix_chains is false and duplicate chain IDs are detected, the node will still ensure uniqueness by appending numeric suffixes and will log a warning.
- Inputs must be dictionaries mapping chain IDs to A3M strings; non-dictionary inputs are ignored.
- Supports up to five A3M inputs.

## Troubleshooting
- Error: 'At least one A3M input is required' — Provide at least one non-empty A3M input.
- Unexpected chain ID overwrites or merges — Enable 'prefix_chains' to prevent conflicts when different inputs reuse the same chain IDs.
- Missing chains in output — Verify each input is a dictionary with expected keys and non-empty A3M strings.
- Downstream node rejects type — Ensure the output is connected where an A3M dictionary is expected, not a raw string.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/a3mcombinernode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

