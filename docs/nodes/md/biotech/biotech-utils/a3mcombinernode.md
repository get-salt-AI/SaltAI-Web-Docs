# A3M Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Combines up to five A3M MSA dictionaries into a single output dictionary. It can optionally prefix chain IDs to avoid naming conflicts and will ensure resulting keys are unique.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/biotech/biotech-utils/a3mcombinernode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node when you have multiple A3M MSA results (e.g., from different searches or partitions) and need a single, merged A3M dictionary for downstream protein analysis or folding. Connect any subset of the five optional A3M inputs; enable chain ID prefixing if different inputs may contain overlapping chain IDs.

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
<tr><td style="word-wrap: break-word;">a3m_1</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">First A3M MSA dictionary to merge. Keys are chain or sequence identifiers; values are A3M-formatted content.</td><td style="word-wrap: break-word;">{'A': '>seq1\nAAAA\n>seq2\nAA-A'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_2</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Second A3M MSA dictionary to merge.</td><td style="word-wrap: break-word;">{'B': '>seq3\nBBBB\n>seq4\nBB-B'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_3</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Third A3M MSA dictionary to merge.</td><td style="word-wrap: break-word;">{'C': '>seq5\nCCCC'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_4</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Fourth A3M MSA dictionary to merge.</td><td style="word-wrap: break-word;">{'A': '>seq6\nAAAA'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_5</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Fifth A3M MSA dictionary to merge.</td><td style="word-wrap: break-word;">{'D': '>seq7\nDDDD'}</td></tr>
<tr><td style="word-wrap: break-word;">prefix_chains</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, prefixes each input’s keys with A3M{i}_ (e.g., A3M1_, A3M2_) to avoid chain ID collisions across inputs.</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_a3m</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">A single A3M dictionary containing all provided entries with unique keys.</td><td style="word-wrap: break-word;">{'A': '>seq1\nAAAA', 'B': '>seq3\nBBBB', 'A3M4_A': '>seq6\nAAAA'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **At least one input is required**: The node raises an error if no A3M inputs are provided.
- **Chain ID conflicts**: If two inputs share the same key and prefix_chains is false, the node will auto-rename duplicates by appending _1, _2, etc. Enabling prefix_chains proactively avoids collisions.
- **Key prefixing**: With prefix_chains enabled, keys from input i are prefixed with A3M{i}_ (e.g., A3M2_A). If a prefixed key still collides, a numeric suffix is appended.
- **Input format**: Each A3M input must be a dictionary mapping IDs to A3M-formatted strings.
- **Merge order**: Inputs are processed in order a3m_1 → a3m_5; when conflicts occur, later items are renamed to keep all entries.

## Troubleshooting
- **Error: 'At least one A3M input is required'**: Connect at least one valid A3M dictionary to a3m_1…a3m_5.
- **Unexpectedly renamed keys (e.g., _1, _2 suffixes)**: This indicates chain ID collisions. Enable prefix_chains or adjust your input keys.
- **Output missing expected entries**: Verify each connected a3m_i contains a non-empty dictionary and that keys are unique or prefixing is enabled.
- **Type mismatch**: Ensure each input is an A3M dictionary {id: a3m_string}. Raw strings should first be loaded/converted to the A3M type using the appropriate loader node.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/biotech/biotech-utils/a3mcombinernode/example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

