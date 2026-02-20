# A3M Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines up to five A3M MSA dictionaries into a single unified A3M dictionary. It ensures unique chain identifiers by optionally prefixing chain IDs per input and, if necessary, appending numeric suffixes to resolve any remaining conflicts.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/a3mcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have multiple A3M MSA inputs (e.g., from different sources or chains) and need a single merged MSA dictionary for downstream structure prediction or analysis. Connect any subset of a3m_1 through a3m_5. Enable prefix_chains to systematically avoid chain ID collisions across inputs.

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
<tr><td style="word-wrap: break-word;">a3m_1</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">First A3M MSA dictionary. Keys are chain IDs; values are A3M content associated with each chain.</td><td style="word-wrap: break-word;">{'A': '<a3m-content-for-chain-A>'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_2</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Second A3M MSA dictionary to merge.</td><td style="word-wrap: break-word;">{'B': '<a3m-content-for-chain-B>'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_3</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Third A3M MSA dictionary to merge.</td><td style="word-wrap: break-word;">{'A': '<a3m-content-for-another-chain-A>'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_4</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Fourth A3M MSA dictionary to merge.</td><td style="word-wrap: break-word;">{'C': '<a3m-content-for-chain-C>'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_5</td><td>False</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">Fifth A3M MSA dictionary to merge.</td><td style="word-wrap: break-word;">{'D': '<a3m-content-for-chain-D>'}</td></tr>
<tr><td style="word-wrap: break-word;">prefix_chains</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, prefixes chain IDs from each input with A3M1_, A3M2_, ... to avoid chain ID conflicts. Numeric suffixes are still added if duplicates remain.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_a3m</td><td style="word-wrap: break-word;">MSA</td><td style="word-wrap: break-word;">A single A3M MSA dictionary containing all chains from the provided inputs with unique chain IDs.</td><td style="word-wrap: break-word;">{'A3M1_A': '<a3m-content-for-chain-A>', 'A3M2_B': '<a3m-content-for-chain-B>', 'A3M3_A_1': '<a3m-content-for-duplicate-A-from-3rd-input>'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- At least one valid A3M input must be provided; otherwise the node raises an error.
- When prefix_chains is disabled and chain IDs collide, the node resolves conflicts by appending numeric suffixes (_1, _2, ...).
- When prefix_chains is enabled, chain IDs are prefixed per input (A3M1_, A3M2_, ...); if collisions still occur, numeric suffixes are added.
- Inputs must be dictionaries mapping chain IDs (strings) to A3M content; non-dictionary inputs are ignored.
- Merging does not concatenate or alter A3M content per chain; it only aggregates entries into one dictionary with unique keys.
- The order of inputs (a3m_1 → a3m_5) determines the A3M# prefix values when prefixing is enabled.

## Troubleshooting
- Error: "At least one A3M input is required" — Provide at least one non-empty A3M dictionary to a3m_1..a3m_5.
- Unexpected chain ID renaming — Enable prefix_chains to systematically prevent collisions across inputs; otherwise, numeric suffixes will be appended automatically.
- Missing chains in output — Ensure each provided a3m_n is a valid dictionary with the expected chain keys and A3M content; non-dictionary values are ignored.
- Confusing chain IDs after merge — Check which input slot each A3M was connected to; with prefix_chains on, the slot defines the A3M# prefix in the output.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/a3mcombinernode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

