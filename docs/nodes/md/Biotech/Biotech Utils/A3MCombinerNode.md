# A3M Combiner

<div style="display: flex; gap: 20px; align-items: flex-start; margin-bottom: 20px;">
<div style="flex: 1; min-width: 0;">

Merges up to five A3M MSA dictionaries into a single combined A3M output. Optionally prefixes chain IDs to avoid collisions and guarantees unique keys by auto-suffixing duplicates.

</div>
<div style="flex: 0 0 300px;"><img src="../../../../images/previews/Biotech/Biotech Utils/A3MCombinerNode.png" alt="Preview" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" /></div>
</div>

## Usage

Use this node to aggregate multiple multiple-sequence alignments (MSAs) prepared separately into one combined A3M input for downstream protein modeling steps. Connect any subset of the a3m_1–a3m_5 inputs; enable prefix_chains to automatically namespace chain IDs per input source.

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
<tr><td style="word-wrap: break-word;">a3m_1</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">First A3M dictionary to include. Keys are chain IDs; values are A3M text for that chain.</td><td style="word-wrap: break-word;">{'A': '>seq1\nAAAA\n>seq2\nAA-A'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_2</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Second A3M dictionary to include.</td><td style="word-wrap: break-word;">{'B': '>seq1\nBBBB\n>seq2\nB-BB'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_3</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Third A3M dictionary to include.</td><td style="word-wrap: break-word;">{'C': '>seq1\nCCCC'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_4</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Fourth A3M dictionary to include.</td><td style="word-wrap: break-word;">{'A': '>seq1\nAAAA'}</td></tr>
<tr><td style="word-wrap: break-word;">a3m_5</td><td>False</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">Fifth A3M dictionary to include.</td><td style="word-wrap: break-word;">{'D': '>seq1\nDDDD'}</td></tr>
<tr><td style="word-wrap: break-word;">prefix_chains</td><td>False</td><td style="word-wrap: break-word;">BOOLEAN</td><td style="word-wrap: break-word;">If true, prefixes chain IDs with the input index (A3M1_, A3M2_, etc.) to avoid collisions. When false, collisions are resolved by appending _1, _2, ...</td><td style="word-wrap: break-word;">True</td></tr>
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
<tr><td style="word-wrap: break-word;">combined_a3m</td><td style="word-wrap: break-word;">A3M</td><td style="word-wrap: break-word;">A single A3M dictionary containing all merged entries with unique chain IDs.</td><td style="word-wrap: break-word;">{'A3M1_A': '>seq1\nAAAA\n>seq2\nAA-A', 'A3M2_B': '>seq1\nBBBB'}</td></tr>
</tbody>
</table>
</div>

## Important Notes
- **At least one A3M input is required**: The node raises an error if no valid A3M dictionaries are provided.
- **Input format**: Each A3M input must be a dictionary mapping chain IDs (e.g., A, B) to A3M-formatted strings.
- **Chain ID conflicts**: If prefix_chains is true, chain IDs are prefixed (A3M{i}_) and any remaining conflicts get numeric suffixes. If false, conflicts trigger a warning and are resolved by auto-appending _1, _2, ...
- **Partial inputs allowed**: You can connect any subset of a3m_1–a3m_5; disconnected inputs are ignored.
- **Non-dict values are ignored**: Inputs that are not dictionaries are skipped without combining.

## Troubleshooting
- **Error: 'At least one A3M input is required'**: Connect at least one valid A3M dictionary to a3m_1–a3m_5.
- **Unexpected chain overwrites or duplicates**: Enable prefix_chains to automatically namespace chain IDs per input and avoid collisions.
- **Wrong input type (e.g., string instead of dict)**: Ensure each A3M input is a dictionary like {"A": "<A3M content>"}. Non-dicts are ignored.
- **Missing chains in output**: Verify the input keys are the expected chain IDs and that each value contains non-empty A3M text.

## Example Pipelines

<figure style="margin: 20px 0;">
  <img src="../../../../images/assets/Biotech/Biotech Utils/A3MCombinerNode/Example.png" alt="Example" style="width: 100%; border-radius: 8px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);" />
  <figcaption style="margin-top: 8px; text-align: center; font-style: italic; color: #666;">Example</figcaption>
</figure>

